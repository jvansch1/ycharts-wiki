## Nature of the problem
Performing any DDL operation on large tables in MySQL is very time-intensive. Therefore, when faced with having to add a column to YCharts calc tables, one has 2 options:

1. (Bad) Preserve table data, and perform a long (1+ hours) migration. This has the negative effect of forcing extended downtime. Moreover, if this process fails for any reason, all changes are rolled back, and downtime duration is multiplied.

2. (Good) `TRUNCATE` the table, perform a reasonably fast migration, and repopulate the table(s) by recalculating the data. For YCharts, it currently makes more sense to follow this approach. It minimizes downtime and can benefit from horizontal scaling (to a degree). 

## Do I need to worry about this?
To get a rundown of the calc tables, this command can be helpful. It will give you a list of the 20 largest calc tables. 
```
SELECT CONCAT(table_schema, '.', table_name),        
    CONCAT(ROUND(table_rows / 1000000, 2), 'M')                                    rows,        
    CONCAT(ROUND(data_length / ( 1024 * 1024 * 1024 ), 2), 'G')                    DATA,        
    CONCAT(ROUND(index_length / ( 1024 * 1024 * 1024 ), 2), 'G')                   idx,        
    CONCAT(ROUND(( data_length + index_length ) / ( 1024 * 1024 * 1024 ), 2), 'G') total_size,        
    ROUND(index_length / data_length, 2)                                           idxfrac 
FROM information_schema.TABLES 
WHERE table_name like 'calculations%' 
ORDER  BY data_length + index_length 
DESC LIMIT  20;
```

```
+------------------------------------------------------------+--------+--------+-------+------------+---------+
| CONCAT(table_schema, '.', table_name)                      | rows   | DATA   | idx   | total_size | idxfrac |
+------------------------------------------------------------+--------+--------+-------+------------+---------+
| ycharts.calculations_mutualfunddailyperformancecalc        | 93.91M | 29.95G | 6.26G | 36.21G     |    0.21 |
| ycharts.calculations_indexdailyperformancecalc             | 86.55M | 19.32G | 3.40G | 22.72G     |    0.18 |
| ycharts.calculations_mutualfunddailytechnicalcalc          | 99.10M | 14.42G | 6.29G | 20.71G     |    0.44 |
| ycharts.calculations_mutualfundannualizeddailyreturncalc   | 95.76M | 15.49G | 4.89G | 20.38G     |    0.32 |
| ycharts.calculations_companydailyperformancecalc           | 47.07M | 15.69G | 2.33G | 18.01G     |    0.15 |
| ycharts.calculations_indexdailytechnicalcalc               | 88.98M | 11.96G | 3.33G | 15.29G     |    0.28 |
| ycharts.calculations_dailybasiccalc                        | 43.05M | 8.10G  | 3.41G | 11.51G     |    0.42 |
| ycharts.calculations_indexannualizeddailyreturncalc        | 83.37M | 7.94G  | 3.34G | 11.28G     |    0.42 |
| ycharts.calculations_companydailytechnicalcalc             | 44.10M | 8.23G  | 2.94G | 11.17G     |    0.36 |
| ycharts.calculations_companyannualizeddailyreturncalc      | 46.48M | 6.75G  | 1.89G | 8.64G      |    0.28 |
| ycharts.calculations_mutualfundmonthlyreturncalc           | 4.10M  | 1.54G  | 0.43G | 1.97G      |    0.28 |
| ycharts.calculations_mutualfundannualizedmonthlyreturncalc | 4.18M  | 1.50G  | 0.47G | 1.96G      |    0.31 |
| ycharts.calculations_indexmonthlyriskcalc                  | 4.53M  | 1.77G  | 0.15G | 1.93G      |    0.09 |
| ycharts.calculations_mutualfundmonthlyriskcalc             | 4.44M  | 1.68G  | 0.15G | 1.83G      |    0.09 |
| ycharts.calculations_companydailynavreturncalc             | 6.09M  | 1.07G  | 0.60G | 1.67G      |    0.56 |
| ycharts.calculations_indexannualizedmonthlyreturncalc      | 5.10M  | 0.91G  | 0.45G | 1.35G      |    0.49 |
| ycharts.calculations_companyannualizedmonthlyreturncalc    | 3.00M  | 0.90G  | 0.27G | 1.17G      |    0.29 |
| ycharts.calculations_companyannualizeddailynavreturncalc   | 5.83M  | 0.60G  | 0.48G | 1.08G      |    0.81 |
| ycharts.calculations_companymonthlyriskcalc                | 2.70M  | 0.97G  | 0.09G | 1.05G      |    0.09 |
| ycharts.calculations_mutualfundmonthlybetacalc             | 6.33M  | 0.61G  | 0.33G | 0.95G      |    0.54 |
+------------------------------------------------------------+--------+--------+-------+------------+---------+
(ran on 11/16/2017 against staging DB)
```

Any table with 10M+ rows should probably follow Approach #2. Also, keep in mind that this scales linearly -- each table being operated on adds to the total time. 

## Operating on medium-sized table but many columns
Another consideration is that django's `migration.AddField` scales linearly with number of columns - it generates a DDL operation _per field_. That means if you are adding 2 columns, it takes twice as long as a single column. 

In fact, `AddField` typically generates 2 DDL operations... For example:
```
migrations.AddField(
    model_name='companydailyperformancecalc',
    name='price_target_upside',
    field=models.DecimalField(decimal_places=4, max_digits=13, null=True),
)
```
becomes
```
ALTER TABLE `calculations_companydailyperformancecalc` ADD COLUMN `price_target_upside` numeric(13, 4) NULL;
ALTER TABLE `calculations_companydailyperformancecalc` ALTER COLUMN `price_target_upside` DROP DEFAULT;
```
To mitigate this linear scaling, consider using `ycharts.utils.migration_utils.AddFields`. It batches `ADD COLUMN` operations together into a single `ALTER TABLE` statement.

# Execution of Strategy #2

### 1. Pick a good time (i.e. the least-worst time) to do this.
 - day where it's most acceptable for calc data to be unavailable. This is generally on Saturday.
 - time window when other tasks are not adding items to the `main_queue`. Check AWS CloudWatch for historical data. Currently, 8pm is the best candidate.

### 2. Write the migration
- `TRUNCATE` the table(s) in question (can use `ycharts.utils.migration_utils.TruncateTable` or `migrations.RunSQL`)
- perform the DDL operation (via `migrations.AddField` or `migration_utils.AddFields`). If you truncate the table, you can use the former because the difference in execution time is negligible.

### 3. Run a [release](https://github.com/ycharts/ycharts_systems/wiki/Deploy-and-Hotfix-%5BYCharts%5D)
- obviously, all systems will be down during this period.

### 4. Repopulate the truncated tables by re-calculating the data
- queue up the cals in a particular order
#### 1. `Indices` of the `US_BROAD_ASSET_CLASSES` & `S&P500`
```
US_BROAD_ASSET_CLASS_TO_INDEX = {
    'US Equity': '^SPXTR',
    'International Equity': '^MSACXUSNTR',
    'Municipal Bond': '^BBMBTR',
    'Allocation': '^SPXTR',
    'Taxable Bond': '^BBUSATR',
    'Commodities': '^STRB',
    'Money Market': '^STRB',
    'Sector Equity': '^MSWNTR',
    'Alternative': '^MSACWINTR',
    'S&P': '^SPX'
}
```
#### 2. `Company`, ordered by `market_cap_usd` DESC, first 1000
```
FIRST_COMPANY_BATCH_SIZE = 1000
first_company_batch = Company.objects.order_by('-market_cap_usd')[:FIRST_COMPANY_BATCH_SIZE]
```
#### 3. `Mutual Fund`, ordered by `assets_under_management_usd` DESC, first 2000
```
FIRST_MUTUAL_FUND_BATCH_SIZE = 2000
first_mutual_fund_batch = MutualFund.objects.order_by('-assets_under_management_usd')[:FIRST_MUTUAL_FUND_BATCH_SIZE]
```
#### 4. The rest of Companies
```
processed_company_ids = first_company_batch.values_list('id', flat=True)
rest = Company.objects\
        .exclude(company_id__in=processed_company_ids)\
        .order_by('-market_cap_usd')
```
#### 5. The rest of Mutual Funds
```
processed_mutual_fund_ids = first_mutual_fund_batch.values_list('id', flat=True)
rest = MutualFund.objects\
        .exclude(mutual_fund_id__in=processed_mutual_fund_ids)\
        .order_by('-assets_under_management_usd'):
```
#### 6. the rest of Indices
```
rest = Index.objects\
        .exclude(symbol__in=US_BROAD_ASSET_CLASS_TO_INDEX.values())
```
- ideally, this can be encapsulated in one or more `onetime_scripts`
```
screen -d -m python /sites/ycharts/<app_name>/onetime_scripts/<script_name.py>
```

- if the calculation is exposed as a management command at a sufficiently granular level, it can be invoked directly. For example:
```
screen -d -m python /sites/ycharts/manage.py companies_tasks_run --task_type monthlyriskcalc
screen -d -m python /sites/ycharts/manage.py indices_tasks_run --task_type monthlyriskcalc
screen -d -m python /sites/ycharts/manage.py mutual_funds_tasks_run --task_type monthlyriskcalc
screen -d -m python /sites/ycharts/apps/mutual_funds/onetime_scripts/reset_can_funds.py
```

### 5. Go to the calc pages in question and make sure that their values are being repopulated. Do not skip this part. 




# Execution on 11/18/2017
1. merge `rc/performance-calcs` into `master`
- this includes `apps.calculations.migrations.0012_add_new_daily_performance_calc_fields`
- `python manage.py sqlmigrate calculations 0012` reveals
```
BEGIN;
TRUNCATE `calculations_companydailyperformancecalc`;
ALTER TABLE `calculations_companydailyperformancecalc`
             ADD COLUMN `mtd_return` numeric(13, 4) NULL, 
             ADD COLUMN `mtd_total_return` numeric(13, 4) NULL, 
             ADD COLUMN `qtd_return` numeric(13, 4) NULL, 
             ADD COLUMN `qtd_total_return` numeric(13, 4) NULL, 
             ADD COLUMN `fifteen_year_return` numeric(13, 4) NULL, 
             ADD COLUMN `fifteen_year_total_return` numeric(13, 4) NULL, 
             ADD COLUMN `twenty_year_return` numeric(13, 4) NULL, 
             ADD COLUMN `twenty_year_total_return` numeric(13, 4) NULL, 
             ADD COLUMN `all_time_return` numeric(13, 4) NULL, 
             ADD COLUMN `all_time_total_return` numeric(13, 4) NULL;

ALTER TABLE `calculations_companydailyperformancecalclatest`
             ADD COLUMN `mtd_return` numeric(13, 4) NULL, ADD COLUMN `mtd_total_return` numeric(13, 4) NULL, ADD COLUMN `qtd_return` numeric(13, 4) NULL, ADD COLUMN `qtd_total_return` numeric(13, 4) NULL, ADD COLUMN `fifteen_year_return` numeric(13, 4) NULL, ADD COLUMN `fifteen_year_total_return` numeric(13, 4) NULL, ADD COLUMN `twenty_year_return` numeric(13, 4) NULL, ADD COLUMN `twenty_year_total_return` numeric(13, 4) NULL, ADD COLUMN `all_time_return` numeric(13, 4) NULL, ADD COLUMN `all_time_total_return` numeric(13, 4) NULL;

TRUNCATE `calculations_mutualfunddailyperformancecalc`;
ALTER TABLE `calculations_mutualfunddailyperformancecalc`
             ADD COLUMN `mtd_return` numeric(13, 4) NULL, 
             ADD COLUMN `mtd_total_return` numeric(13, 4) NULL, 
             ADD COLUMN `qtd_return` numeric(13, 4) NULL, 
             ADD COLUMN `qtd_total_return` numeric(13, 4) NULL, 
             ADD COLUMN `fifteen_year_return` numeric(13, 4) NULL, 
             ADD COLUMN `fifteen_year_total_return` numeric(13, 4) NULL, 
             ADD COLUMN `twenty_year_return` numeric(13, 4) NULL, 
             ADD COLUMN `twenty_year_total_return` numeric(13, 4) NULL, 
             ADD COLUMN `all_time_return` numeric(13, 4) NULL, 
             ADD COLUMN `all_time_total_return` numeric(13, 4) NULL;

ALTER TABLE `calculations_mutualfunddailyperformancecalclatest`
             ADD COLUMN `mtd_return` numeric(13, 4) NULL, ADD COLUMN `mtd_total_return` numeric(13, 4) NULL, ADD COLUMN `qtd_return` numeric(13, 4) NULL, ADD COLUMN `qtd_total_return` numeric(13, 4) NULL, ADD COLUMN `fifteen_year_return` numeric(13, 4) NULL, ADD COLUMN `fifteen_year_total_return` numeric(13, 4) NULL, ADD COLUMN `twenty_year_return` numeric(13, 4) NULL, ADD COLUMN `twenty_year_total_return` numeric(13, 4) NULL, ADD COLUMN `all_time_return` numeric(13, 4) NULL, ADD COLUMN `all_time_total_return` numeric(13, 4) NULL;

TRUNCATE `calculations_indexdailyperformancecalc`;
ALTER TABLE `calculations_indexdailyperformancecalc`
             ADD COLUMN `mtd_return` numeric(13, 4) NULL, 
             ADD COLUMN `qtd_return` numeric(13, 4) NULL, 
             ADD COLUMN `fifteen_year_return` numeric(13, 4) NULL, 
             ADD COLUMN `twenty_year_return` numeric(13, 4) NULL, 
             ADD COLUMN `all_time_return` numeric(13, 4) NULL;

ALTER TABLE `calculations_indexdailyperformancecalclatest`
             ADD COLUMN `mtd_return` numeric(13, 4) NULL, ADD COLUMN `qtd_return` numeric(13, 4) NULL, ADD COLUMN `fifteen_year_return` numeric(13, 4) NULL, ADD COLUMN `twenty_year_return` numeric(13, 4) NULL, ADD COLUMN `all_time_return` numeric(13, 4) NULL;

TRUNCATE `calculations_companydailynavreturncalc`;
ALTER TABLE `calculations_companydailynavreturncalc`
             ADD COLUMN `mtd_total_return` numeric(13, 4) NULL, 
             ADD COLUMN `qtd_total_return` numeric(13, 4) NULL, 
             ADD COLUMN `fifteen_year_total_return` numeric(13, 4) NULL, 
             ADD COLUMN `twenty_year_total_return` numeric(13, 4) NULL, 
             ADD COLUMN `all_time_total_return` numeric(13, 4) NULL;

ALTER TABLE `calculations_companydailynavreturncalclatest`
             ADD COLUMN `mtd_total_return` numeric(13, 4) NULL, 
             ADD COLUMN `qtd_total_return` numeric(13, 4) NULL, 
             ADD COLUMN `fifteen_year_total_return` numeric(13, 4) NULL, 
             ADD COLUMN `twenty_year_total_return` numeric(13, 4) NULL, 
             ADD COLUMN `all_time_total_return` numeric(13, 4) NULL;

COMMIT;
```
2. follow regular [production deployment procedures](https://github.com/ycharts/ycharts_systems/wiki/Deploy-and-Hotfix-%5BYCharts%5D)
3. SSH into `main_admin` and execute
```
screen -d -m python /sites/ycharts/calculations/onetime_scripts/calculations_reset_performance_calcs_all.py
```
4. observe 
- the [main queue](https://ycharts.com/systems/review/queue_info) 
- [company calc data](https://ycharts.com/companies/MSFT/price)
- [mutual fund calc data](https://ycharts.com/mutual_funds/M:DYN2569.TO/price)
- [index calc data](https://ycharts.com/indices/%5ESPX/level).



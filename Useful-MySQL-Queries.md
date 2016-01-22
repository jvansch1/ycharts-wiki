### Sysadmin

```mysql
# Get rough table / index size of all tables
SELECT table_name, table_rows,
    ROUND(((data_length ) / 1024 / 1024),2) as table_size,
    ROUND(((index_length) / 1024 / 1024),2) as index_size,
    ROUND(((data_length + index_length) / 1024 / 1024),2) as total_size
FROM information_schema.TABLES
WHERE table_schema = "ycharts" order by total_size desc;

# Look at collations/charsets of all tables
SELECT table_schema, table_name, character_set_name, table_collation
FROM information_schema.TABLES
INNER JOIN information_schema.collation_character_set_applicability
ON (TABLES.table_collation = collation_character_set_applicability.collation_name)
WHERE table_schema NOT IN ('mysql', 'information_schema');

# Look at running queries
SHOW processlist;

# Kill tasks that are metalocking a database
SHOW processlist;
kill <bad pid num>;

# Output to csv
SELECT foo FROM bar INTO OUTFILE '<file>'
FIELDS TERMINATED BY ',' ENCLOSED BY '"'
LINES TERMINATED BY '\n';
```

```bash
# Output to csv (RDS friendly)
mysql --host=production-2015-05-16.cy4wtovspprb.us-east-1.rds.amazonaws.com -p -u ycharts -D ycharts --batch -e "QUERY_QUERY_QUERY" |     sed 's/\t/","/g;s/^/"/;s/$/"/;s/\n//g' > ~/XXX.csv

# ON local machine, move CSV output over
scp production:~/XXX.csv ~/.
```

## Companies

```mysql
# Overall rating distribution
SELECT overall_rating, COUNT(*) FROM companies_company
GROUP BY overall_rating ORDER BY overall_rating;

# Attractive rating distribution by sector
SELECT s.name, COUNT(*) FROM companies_company AS c
JOIN industries_industry AS i ON (c.industry_id = i.id)
JOIN sectors_sector AS s ON (i.sector_id = s.id)
WHERE c.overall_rating = 'attractive'
GROUP BY s.name;

# Attractive rating distribution by industry
SELECT i.name, COUNT(*) FROM companies_company AS c
JOIN industries_industry AS i ON (c.industry_id = i.id)
WHERE c.overall_rating = 'attractive'
GROUP BY i.name;

# Empty market caps
SELECT quarter_marker, marker_quarter_end, COUNT(*) FROM calculations_morndailybasiccalclatest
WHERE market_cap IS NULL
GROUP BY quarter_marker, market_quarter_end;

# Stock ticks group by date
SELECT DATE(quote_date), COUNT(*) FROM companies_stocktick
GROUP BY DATE(quote_date) ORDER BY quote_date;

# Companies >50M in market cap that don't have IR Urls
SELECT exchange_symbol, name FROM companies_company AS c
JOIN investor_relations_companyinvestorinfo AS i ON c.investor_info_id = i.id
WHERE i.ir_url = '' AND c.market_cap_usd > 50
    AND c.is_primary_share_in_universe
    AND c.exchange_id IN (
        SELECT id FROM exchanges_exchange
        WHERE exchange_code NOT IN ('OTCBB', 'GREY', 'PINK')
    )
ORDER BY c.market_cap_usd DESC;
```

## Indicators

```mysql
# Indicators grouped by frequency
SELECT frequency, COUNT(*) FROM indicators_indicator
GROUP BY frequency ORDER BY frequency;

# Indicators grouped by data type
SELECT data_type, COUNT(*) FROM indicators_indicator
GROUP BY data_type ORDER BY data_type;

# Indicators grouped by unit
SELECT data_type, unit, unit_shorthand, COUNT(*) FROM indicators_indicator
GROUP BY data_type, unit, unit_shorthand ORDER BY data_type, unit;
```

## Users

```mysql
# Possible robot accounts
SELECT * FROM auth_user WHERE email RLIKE "^[a-z]{6}@[a-z]{6}\.com";

# Registered users who haven't logged in
# more than a day after they joined
SELECT COUNT(*) FROM auth_user
WHERE last_login < (date_joined + INTERVAL 1 day);
```

## South Manual Migrations Examples
If you ever need to run a manual sql migration

```bash
  indicators/migrations/0167 - update records in a database based on another column
calculations/migrations/0138 - add columns to an existing table
calculations/migrations/0163 - add, drop, and modify column attributes on a table
  financials/migrations/0038 - modify true/false boolean attributes on existing columns
```

You can also do a grep for db.execute to find other manual migrations
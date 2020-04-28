## Sysadmin

```mysql
# Get rough table / index size of all tables
SELECT table_name, table_rows,
    ROUND(((data_length ) / 1024 / 1024),2) as table_size,
    ROUND(((index_length) / 1024 / 1024),2) as index_size,
    ROUND(((data_length + index_length) / 1024 / 1024),2) as total_size
FROM information_schema.TABLES
order by total_size desc;

# Total disk usage of all tables (not just ycharts)
SELECT SUM(((data_length ) / 1024 / 1024)) as table_size,
    SUM(((index_length) / 1024 / 1024)) as index_size,
    SUM(((data_length + index_length) / 1024 / 1024)) as total_size
FROM information_schema.TABLES;

# Look at collations/charsets of all tables
SELECT table_schema, table_name, character_set_name, table_collation
FROM information_schema.TABLES
INNER JOIN information_schema.collation_character_set_applicability
ON (TABLES.table_collation = collation_character_set_applicability.collation_name)
WHERE table_schema NOT IN ('mysql', 'information_schema');

# Look at running queries
SHOW full processlist;

# Kill tasks that are metalocking a database
SHOW processlist;
kill <bad pid num>;
```

## Export 

### Export query results to CSV
```mysql
SELECT foo FROM bar INTO OUTFILE '<file>'
FIELDS TERMINATED BY ',' ENCLOSED BY '"'
LINES TERMINATED BY '\n';
```

### Export query results to CSV (RDS friendly)
```bash
# Export results of querty
mysql --host=DATABASE_HOST_NAME m -p -u ycharts -D ycharts --batch -e "QUERY_QUERY_QUERY" |     sed 's/\t/","/g;s/^/"/;s/$/"/;s/\n//g' > ~/XXX.csv

# On local machine, move CSV output over
cd ~
scp -o ProxyCommand="ssh ec2-user@NAT_MACHINE_ADDRESS -W %h:%p" ubuntu@MACHINE_PRIVATE_IP:~/XXX.csv .
```

### Backup and Restore a Single Database Table

```bash
mysqldump -u ycharts -p -D ycharts TABLE_NAME > TABLE_NAME.sql
mysql -u ycharts -p  -D ycharts < TABLE_NAME.sql
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

## Indices
```mysql
# Differen data provider combos
select intraday_data_provider, ohlc_data_provider, close_data_provider, count(*) from indices_index group by intraday_data_provider, ohlc_data_provider, close_data_provider order by intraday_data_provider, ohlc_data_provider, close_data_provider;

# Different family / subfamily combos
select family, subfamily, count(*) from indices_index group by family, subfamily order by family, subfamily;

# Different provider / family / subfamily combos
select provider, family, subfamily, count(*) from indices_index group by provider, family, subfamily order by provider, family, subfamily;
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

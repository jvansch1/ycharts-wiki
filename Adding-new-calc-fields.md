# Nature of the problem
Performing any DDL operation on large tables in MySQL is very time-intensive. Therefore, when faced with having to add a column to YCharts calc tables, one has 2 options:

1. (Bad) Preserve table data, and perform a long (1+ hours) migration. This has the negative effect of forcing extended downtime. Moreover, if this process fails for any reason, all changes are rolled back, and downtime duration is multiplied.

2. (Good) `TRUNCATE` the table, perform a reasonably fast migration, and repopulate the table(s) by recalculating the data. For YCharts, it currently makes more sense to follow this approach. It minimizes downtime and can benefit from horizontal scaling (to a degree). 

# Do I need to worry about this?
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

Any table with 10M+ rows should probably follow Approach #2. Also, keep in mind that this scales linearly -- each table being operated on adds to the total time. 



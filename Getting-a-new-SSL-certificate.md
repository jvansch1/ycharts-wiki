Everytime we need to update our SSL certificate we need to take the following steps AFTER we buy it.

1) Generate the private key
```
openssl genrsa -out ycharts.com.key 2048
```

2) Generate the CSR using the private key
```
 openssl req -sha256 -new -key ycharts.com.key -out ycharts.com.csr
```

Answer to Questions:

| Question        | Answer              |
| ------------- |:---------------------:|
| Country Name  | US                    |
| State         | Illinois              |
| City          | Chicago               |
| Org           | YCharts, Inc.         |
| Common Name   | LEAVE BLANK           |
| Email         | LEAVE BLANK           |
| Extra Stuff   | LEAVE BLANK           |


3) Now upload the CSR to the cert provider.

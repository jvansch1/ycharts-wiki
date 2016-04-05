# To Diagnose Memcache Issues


```
ssh production_admin
# Figure out the box location you might want ... but they should be the same
telnet prod-cache-vpc.sjqohv.0003.use1.cache.amazonaws.com 11211
stats items

stats cachedump <slabnumber> <number of keys>
# For instance ...
stats cachedump 12 100
```

That will give you something like
```
ITEM :1:throttle_user_59.167.194.83 [1060 b; 1459817203 s]
ITEM :1:throttle_user_38.101.184.210 [880 b; 1459801108 s]
ITEM :1:apps.companies.executor.companytask._get_raw_calculation_for_range:66;zoetisinc(zts),assets_annual,2004-12-31,2016-04-05; [760 b; 1459787549 s]
ITEM :1:apps.companies.executor.companytask._get_raw_calculation_for_range:66;zoetisinc(zts),net_income_annual,2004-12-31,2016-04-05; [808 b; 1459787549 s]
```

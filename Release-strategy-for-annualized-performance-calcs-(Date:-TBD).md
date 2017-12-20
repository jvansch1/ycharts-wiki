### Before release
- January 6th?
- once date is set, create status page for scheduled maintenance.

### Day of release
#### 1. prepare code
- merge `rc/feature/annualized-performance` into `master`
- merge `master` into `develop`
- (optional but definitely good idea): staging release

#### 2. `production` release
- follow steps in `ycharts_systems` wiki
- pray

#### 3. post-release
- ssh to `main_admin`
- run `onetime_script` to recalculate all `AnnualizedDaily` calcs
     - `python /sites/ycharts/apps/calculations/onetime_scripts/calculations_reset_annualized_perf_calcs_all.py`
- check queue to make sure jobs are being picked up
- perform sanity checks for `Company`, `MutualFund` and `Index` calcs


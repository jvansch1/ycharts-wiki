## Fabric Monitor
Run `fab production monitor` to make sure all the machines are running as expected.
- What `fab monitor` does it is actually SSHs into each machine and then runs `initctl` and checks that the services that should be running are in a running state.

## AWS Common Issues
These are a list of common issues when are having problems with AWS related systems.

* Check that autoscaling is up
* Check that the autoscaling policies are in place
* Check that the Machine CPU usage is normal
* SSH into a machine and run `top`
* Check fab and chef logs

## Log Locations

[Command Dashboard](https://ycharts.com/systems/review/important_command_dashboard/)

[Sentry](https://sentry.io/ycharts/)

| Service | Log Location |
| --- | --- |
| uWSGI | ```/var/log/uwsgi/uwsgi.log``` |
| nginx | ```/var/log/nginx/*``` |
| fab | ```/var/log/chef/fab-run.log``` |
| chef | ```/var/log/chef/chef-run.log``` |
| sys | ```/var/log/syslog``` |
| Long Running Commands | ```/sites/ycharts/data/logs/*``` |




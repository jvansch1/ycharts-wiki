## Overview

This guide goes over the necessary process to switch our IP addresses across staging and production. The general steps are as follows:
1. Allocate new IP addresses.
2. Send list of new IPs to relevant organizations so they can whitelist them first.
3. Once we confirm that they are whitelisted, one by one associate the IPs to our NAT servers.
4. Update the new IPs in other various places in our code (Fab file, SSH Config, etc..)

### 1. Allocate new IP Address
1. Login to AWS
2. Select "EC2"
<br>![](https://www.diigo.com/file/image/sosopsazdccdpappdzcdppqbor/AWS+Management+Console.jpg)
3. Select "Elastic IPs"
<br>![](https://www.diigo.com/file/image/sosopsazdccdpasebzcdppqbpq/EC2+Management+Console.jpg)
4. Allocate 3 New IP addresses by selecting "Allocate New Address" and Make sure to select used in "VPC"
<br>![](https://www.diigo.com/file/image/sosopsazdccdpbcpazcdppqbqb/EC2+Management+Console.jpg)


### 2. Notify organizations of new IPs
Send an email to the below organizations to whitelist our 3 new IPs that were allocated.
1. Morningstar
2. Bank of Dallas
3. [Insert other if found]


### 3. Associate new IPs with NAT servers
1. Login to AWS
2. Select "EC2"
<br>![](https://www.diigo.com/file/image/sosopsazdccdpappdzcdppqbor/AWS+Management+Console.jpg)
3. Select "Elastic IPs"
<br>![](https://www.diigo.com/file/image/sosopsazdccdpasebzcdppqbpq/EC2+Management+Console.jpg)
4. One by one select each Elastic IP and associate it to a NAT sever (`production_public_subnet_d_nat`, `production_public_subnet_e_nat` and `staging_public_subnet_d_nat`)
<br>![](https://www.diigo.com/file/image/sosopsazdccdpcppszcdppqbra/EC2+Management+Console.jpg)


### 4. Update various places with our new IPs
1. In our `fabfil.py`
```python
@task
def production(roles=None, scaled_instance=None, skip=None):
    puts('Environment set to {0}'.format(green('PRODUCTION')))

    env.environment = 'production'
    env.gateway = 'ec2-user@52.4.31.32' # <--- TODO: UPDATE THIS TO PRODUCTION SUBNET D IP ADDRESS
    env.skips = skip.split(',') if skip is not None else []
    env.scaled_instance = scaled_instance is not None

    if 'git_checks' not in env.skips:
        check_git_branch('master')
        if not confirm('Are you sure you want to deploy to PRODUCTION?', default=False):
            abort('Deployment aborted by user.')
        if not confirm('Did you check with Sales and make sure there are no active client demos?', default=False):
            abort('Please HOLD OFF on deploying to production until you confirm there are no active client demos.')

    _get_hosts_for_roles(roles)
```
Update `env.gateway = 'ec2-user@52.4.31.32'` to 'ec2-user@[NEW Production Subnet D IP]'
2. In `fabfile.py`
```python
@task
def staging(roles=None, scaled_instance=None, skip=None):
    puts('Environment set to {0}'.format(green('STAGING')))

    env.environment = 'staging'
    env.gateway = 'ec2-user@52.6.96.159' # <--- TODO: UPDATE THIS TO STAGING SUBNET D IP ADDRESS
    env.skips = skip.split(',') if skip is not None else []
    env.scaled_instance = scaled_instance is not None

    if 'git_checks' not in env.skips:
        check_git_branch('develop')

    _get_hosts_for_roles(roles)

```
3. In `ssh.conf` update each entry to the correct IP Address 

### If A Bad Zip File Is Downloaded from fab [environment] build
```
[10.0.2.197] out:     unpack_file(from_path, location, content_type, link)
[10.0.2.197] out:   File "/home/ubuntu/.virtualenvs/ycharts/lib/python3.4/site-packages/pip/utils/__init__.py", line 598, in unpack_file
[10.0.2.197] out:     flatten=not filename.endswith('.whl')
[10.0.2.197] out:   File "/home/ubuntu/.virtualenvs/ycharts/lib/python3.4/site-packages/pip/utils/__init__.py", line 483, in unzip_file
[10.0.2.197] out:     zip = zipfile.ZipFile(zipfp, allowZip64=True)
[10.0.2.197] out:   File "/usr/lib/python3.4/zipfile.py", line 937, in __init__
[10.0.2.197] out:     self._RealGetContents()
[10.0.2.197] out:   File "/usr/lib/python3.4/zipfile.py", line 978, in _RealGetContents
[10.0.2.197] out:     raise BadZipFile("File is not a zip file")
[10.0.2.197] out: zipfile.BadZipFile: File is not a zip file
[10.0.2.197] out: You are using pip version 8.0.2, however version 8.1.2 is available.
[10.0.2.197] out: You should consider upgrading via the 'pip install --upgrade pip' command.
```

If you see badzipfile ... it means pip downloaded a corrupted zipfile. ssh into the production_build machine and delete the bad zip/wheel file and run build again.

### If you get a SSH protocol error
```
!!! Parallel execution exception under host u'10.0.3.26':
Process 10.0.3.26:
Traceback (most recent call last):
  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/multiprocessing/process.py", line 258, in _bootstrap
    self.run()
  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/multiprocessing/process.py", line 114, in run
    self._target(*self._args, **self._kwargs)
  File "/Users/kevinfox/.virtualenvs/ycharts/src/fabric/fabric/tasks.py", line 243, in inner
    submit(task.run(*args, **kwargs))
  File "/Users/kevinfox/.virtualenvs/ycharts/src/fabric/fabric/tasks.py", line 175, in run
    return self.wrapped(*args, **kwargs)
  File "/Users/kevinfox/.virtualenvs/ycharts/src/fabric/fabric/decorators.py", line 182, in inner
    return func(*args, **kwargs)
  File "/sites/ycharts/fabfile.py", line 808, in _start
    env.host_configs[env.host_string].start()
  File "/sites/ycharts/fabfile.py", line 409, in start
    super(IndicatorsAdminHostConfig, self).start()
  File "/sites/ycharts/fabfile.py", line 63, in start
    self.sudo('start {0}'.format(script))
  File "/Users/kevinfox/.virtualenvs/ycharts/src/fabric/fabric/network.py", line 656, in host_prompting_wrapper
    return func(*args, **kwargs)
  File "/Users/kevinfox/.virtualenvs/ycharts/src/fabric/fabric/operations.py", line 1114, in sudo
    stderr=stderr, timeout=timeout, shell_escape=shell_escape,
  File "/Users/kevinfox/.virtualenvs/ycharts/src/fabric/fabric/operations.py", line 928, in _run_command
    channel=default_channel(), command=wrapped_command, pty=pty,
  File "/Users/kevinfox/.virtualenvs/ycharts/src/fabric/fabric/state.py", line 397, in default_channel
    chan = _open_session()
  File "/Users/kevinfox/.virtualenvs/ycharts/src/fabric/fabric/state.py", line 389, in _open_session
    return connections[env.host_string].get_transport().open_session()
  File "/Users/kevinfox/.virtualenvs/ycharts/src/fabric/fabric/network.py", line 158, in __getitem__
    self.connect(key)
  File "/Users/kevinfox/.virtualenvs/ycharts/src/fabric/fabric/network.py", line 150, in connect
    user, host, port, cache=self, seek_gateway=seek_gateway)
  File "/Users/kevinfox/.virtualenvs/ycharts/src/fabric/fabric/network.py", line 438, in connect
    sock = get_gateway(host, port, cache, replace=tries > 0)
  File "/Users/kevinfox/.virtualenvs/ycharts/src/fabric/fabric/network.py", line 92, in get_gateway
    cache[gateway] = connect(*normalize(gateway) + (cache, False))
  File "/Users/kevinfox/.virtualenvs/ycharts/src/fabric/fabric/network.py", line 478, in connect
    raise NetworkError(msg, e)
NetworkError: Error reading SSH protocol banner
```
1. Find the Host that failed by either looking at the stacktrace (in this case `10.0.3.26`).
2. Go to EC2 Management Console and look at instances.
3. Search for that IP and see what machine it is (in this case it was `indicators_admin`) and it failed on the start command
4. Re start the machine by running `fab production:indicators_admin start`
5. If you can ssh into the machine, find out what type of machine it corresponds to and run the stop command for that type, potentially `fab production:companies_admin stop`. (You might also need to run deploy on the admin machines separately)



### If Autoscaling error at end of deploy
```
[10.0.2.159] run: crontab /sites/ycharts/confs/cron/admin_cron.txt
[10.0.2.159] Enabling Web Autoscaling
[10.0.2.159] Enabling Queue Autoscaling
Traceback (most recent call last):
  File "/home/vagrant/.virtualenvs/ycharts/src/fabric/fabric/main.py", line 749, in main
    *args, **kwargs
  File "/home/vagrant/.virtualenvs/ycharts/src/fabric/fabric/tasks.py", line 388, in execute
    multiprocessing
  File "/home/vagrant/.virtualenvs/ycharts/src/fabric/fabric/tasks.py", line 278, in _execute
    return task.run(*args, **kwargs)
  File "/home/vagrant/.virtualenvs/ycharts/src/fabric/fabric/tasks.py", line 175, in run
    return self.wrapped(*args, **kwargs)
  File "/home/vagrant/.virtualenvs/ycharts/src/fabric/fabric/decorators.py", line 140, in decorated
    decorated.return_value = func(*args, **kwargs)
  File "/sites/ycharts/fabfile.py", line 802, in start
    enable_autoscaling()
  File "/home/vagrant/.virtualenvs/ycharts/src/fabric/fabric/tasks.py", line 172, in __call__
    return self.run(*args, **kwargs)
  File "/home/vagrant/.virtualenvs/ycharts/src/fabric/fabric/tasks.py", line 175, in run
    return self.wrapped(*args, **kwargs)
  File "/home/vagrant/.virtualenvs/ycharts/src/fabric/fabric/decorators.py", line 140, in decorated
    decorated.return_value = func(*args, **kwargs)
  File "/sites/ycharts/fabfile.py", line 900, in enable_autoscaling
    QueueAutoscaleContoller(group_types=group_types).enable_autoscaling(hyperscale=hyperscale)
  File "/sites/ycharts/fabfile.py", line 966, in enable_autoscaling
    self._enable_autoscaling()
  File "/sites/ycharts/fabfile.py", line 985, in _enable_autoscaling
    self.aws_helper.scale_groups_resume()
  File "/sites/ycharts/apps/systems/utils/aws_utils.py", line 658, in scale_groups_resume
    self.create_autoscale_alarms('up')
  File "/sites/ycharts/apps/systems/utils/aws_utils.py", line 526, in create_autoscale_alarms
    policies = self.get_autoscale_policies(policy_type)
  File "/sites/ycharts/apps/systems/utils/aws_utils.py", line 425, in get_autoscale_policies
    existing_policies = self.autoscale_connection.get_all_policies()
  File "/home/vagrant/.virtualenvs/ycharts/lib/python3.4/site-packages/boto/ec2/autoscale/__init__.py", line 574, in get_all_policies
    [('member', ScalingPolicy)])
  File "/home/vagrant/.virtualenvs/ycharts/lib/python3.4/site-packages/boto/connection.py", line 1186, in get_list
    raise self.ResponseError(response.status, response.reason, body)
boto.exception.BotoServerError: BotoServerError: 400 Bad Request
<ErrorResponse xmlns="http://autoscaling.amazonaws.com/doc/2011-01-01/">
  <Error>
    <Type>Sender</Type>
    <Code>Throttling</Code>
    <Message>CloudWatchAlarm Rate exceeded</Message>
  </Error>
  <RequestId>b394fd2c-2dcd-11e6-bd1c-59ca2fc669e3</RequestId>
</ErrorResponse>
```
1. `fab production disable_autoscaling:queue`
2. `fab production enable_autoscaling:queue`

###Mysterious AWS 401 Unauthorized Error
```
boto.exception.EC2ResponseError: EC2ResponseError: 401 Unauthorized
<?xml version="1.0" encoding="UTF-8"?>
<Response><Errors><Error><Code>AuthFailure</Code><Message>AWS was not able to validate the provided access credentials</Message></Error></Errors><RequestID>624b9d59-1def-4d71-8bf1-1b11176d98fd</RequestID></Response>
```
What worked for me was ``vagrant halt`` followed by ``vagrant up``
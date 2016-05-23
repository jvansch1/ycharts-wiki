If A Bad Zip File Is Downloaded
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
[10.0.2.197] out: 
```

If you see this, it mean pip downloaded a zipfile that was bad. ssh into the production_build machine and delete the bad zip/wheel file and run build again.
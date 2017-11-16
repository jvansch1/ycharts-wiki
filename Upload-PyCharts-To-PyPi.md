The PyCharts repo is public so keeping it here in YCharts
```
pip install twine
cd /sites/pycharts
# Update version in setup.py
python setup.py bdist_wheel
twine upload dist/<latest version>.wheel --config-file .pypirc
```
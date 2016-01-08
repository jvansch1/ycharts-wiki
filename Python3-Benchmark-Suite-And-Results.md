#### Py3 vs Py2 performance evaluation for the following benchmarks:

```python
    BENCHMARKS = (
        'screenrunner',
        'multisecurity_export_daily',
        'multisecurity_export_quarterly',
        'data_page_daily',
        'data_page_monthly',
        'dashboard_stat',
    )
```

Here is the [benchmark suite file](https://gist.github.com/tiggreen/aa4878dff741cc9c11c2) I ran. Each benchmark is run 10 times.

Here are the results:

### Python 2
```bash
[tigran:/sites/ycharts] [ycharts] python_benchmark(+4/-4)+* 33s ± python manage.py systems_run_py3k_benchmark_suite  
Running each benchmark 10 times
                  screenrunner:   2.20700s ( 0.22070s per run)
    multisecurity_export_daily: 184.93310s (18.49331s per run)
multisecurity_export_quarterly:  14.66133s ( 1.46613s per run)
               data_page_daily:   0.30271s ( 0.03027s per run)
             data_page_monthly:   0.21111s ( 0.02111s per run)
                dashboard_stat:   0.34397s ( 0.03440s per run)
Total Time: 202.65922s
```

### Python 3

```bash
[tigran:/sites/ycharts] [ycharts3.4] python_benchmark(+5/-4)+* 18m38s 1 ± python manage.py systems_run_py3k_benchmark_suite 
Running each benchmark 10 times
                  screenrunner:   1.90512s ( 0.19051s per run)
    multisecurity_export_daily: 144.07197s (14.40720s per run)
multisecurity_export_quarterly:  14.94353s ( 1.49435s per run)
               data_page_daily:   0.23601s ( 0.02360s per run)
             data_page_monthly:   0.17142s ( 0.01714s per run)
                dashboard_stat:   0.31938s ( 0.03194s per run)
Total Time: 161.64744s
```

**On average  20% improvement on these benchmarks.**

You can also `cprofile` this and have it saved in a file. 

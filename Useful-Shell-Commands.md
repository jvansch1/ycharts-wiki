## Move file from AWS instance to your computer

```bash
scp -o ProxyCommand="ssh ec2-user@NAT_GATEWAY_IP -W %h:%p" ubuntu@MACHINE_PRIVATE_IP:~/XXX.csv .
```

## See memory usage of linux processes

```bash
ps -eo pmem,pcpu,rss,vsize,args | sort -k 1 -r | more
```

# Random Sysadmin Notes

## Move file from one AWS instance to another using SCP

    scp -i /home/ubuntu/.ssh/ycharts-2014-01.pem domU-12-31-38-00-61-92:~/ycharts_post_2_3.sql .

# See memory usage of linux processes

    ps -eo pmem,pcpu,rss,vsize,args | sort -k 1 -r | more


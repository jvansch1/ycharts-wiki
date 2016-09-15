## Move file from one AWS instance to another using SCP

    scp -i /home/ubuntu/.ssh/ycharts-2014-01.pem domU-12-31-38-00-61-92:~/ycharts_post_2_3.sql .

## See memory usage of linux processes

    ps -eo pmem,pcpu,rss,vsize,args | sort -k 1 -r | more

## Copy a file from an autoscale machine to your local(example)

   ```scp -o "ProxyCommand='ssh ec2-user@52.4.31.32 -W %h:%p' " ubuntu@10.0.5.102:remote_file.tar.gz ~/new_local_file.tar.gz
# Debugging Running Processes on Ubuntu

Sometimes a process gets stuck for whatever reason and it can be useful to determine why
it is stuck.

## strace

To use strace, find the process id (PID)

```bash
$ ps -ef | grep <PROCESS NAME>
UID        PID  PPID  C STIME TTY          TIME CMD
...
ubuntu   11904     1  0 08:00 ?        00:00:00 /home/ubuntu/.virtualenvs/ycharts/bin/python /sites/ycharts/manage.py main_execute_daily_tasks -v 1 --traceback
...
```

Then run strace on that process to see what system calls it is making

```bash
$ strace -p <PID> -s 1000
```

### recvfrom

One issue we've had is a process hanging on a `recvfrom` on a socket. The output of `strace` will look
like

```bash
recvfrom(14,
```

and be stuck there. That number is the socket number. To determine what the inode number of the socket
is run

```bash
$ ls -alh /proc/<PID>/fd
total 0
dr-x------ 2 root root  0 Aug 21 13:10 .
dr-xr-xr-x 9 root root  0 Aug 21 04:00 ..
...
lrwx------ 1 root root 64 Aug 21 14:18 14 -> socket:[472485784]
...
```

Look for the socket id you are interested in and the inode number will be the number in brackets following
`socket:`. To see what is happening with this socket, now run

```bash
$ netstat -apeen | grep <INODE>
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address        State       User       Inode       PID/Program name
...
tcp        0      0 10.5.153.177:51478      10.227.19.108:6379     ESTABLISHED 0          472485784   11904/python
...
```

From here you can look at the foreign address to see what you are trying to connect to. In our case, we were
connecting to one of our redis servers in `main_execute_daily_tasks`, and running

```bash
$ netstat -apeen | grep <REDIS_IP>
```

showed multiple connections to the same redis server from the same PID. This situation was resolved by using
`gdb` to close the sockets (the one which was hanging) which allowed the program to reconnect only once and
finish.

## gdb

`strace` lets you view system calls. `gdb` is an interactive debugger.

To connect to a running process:

```bash
$ sudo gdp -p <PID>
... Some text here ...
(gdb)
```

To close a socket:

```bash
(gdb) p close(<SOCKET_NUM>)

$1 = 0

(gdb) c

Continuing.
```

Press `Ctrl-D` to exit and now you can run `strace` to see if the program is running again.

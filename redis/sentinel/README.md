# Example Sentinel deployment

- Masters are called M1, M2, M3, ..., Mn.
- Replicas are called R1, R2, R3, ..., Rn (R stands for replica).
- Sentinels are called S1, S2, S3, ..., Sn.
- Clients are called C1, C2, C3, ..., Cn.
- When an instance changes role because of Sentinel actions, we put it inside square brackets, so [M1] means an instance that is now a master because of Sentinel intervention

## Badic structure

```txt
       +----+
       | M1 |
       | S1 |
       +----+
          |
+----+    |    +----+
| R2 |----+----| R3 |
| S2 |         | S3 |
+----+         +----+

Configuration: quorum = 2
```

## M1 configurations

### Download redis

```shell
# wget https://download.redis.io/releases/redis-5.0.14.tar.gz

```

### Install redis

```shell
# tar -zxvf redis-5.0.14.tar.gz
# cd redis-5.0.14
# make
# make install
```

### Config redis Master

```shell
# Copy conf/redis-m.conf /root/redis-5.0.14/redis.conf
# Main keys:
protected-mode yes
pidfile "/var/run/redis_6379.pid"
logfile "/var/log/redis/redis-6379.log"
masterauth "123456"
requirepass 123456
appendonly yes 
```

### Start redis 

```shell
# redis-server /root/redis-5.0.14/redis.conf
```

## R1 configuration

### Download redis

```shell
# wget https://download.redis.io/releases/redis-5.0.14.tar.gz

```

### Install redis

```shell
# tar -zxvf redis-5.0.14.tar.gz
# cd redis-5.0.14
# make
# make install
```

### Config redis Replica

```shell
# Copy conf/redis-r.conf /root/redis-5.0.14/redis.conf
# Main keys:
replicaof <master-ip> 6379
protected-mode yes
pidfile "/var/run/redis_6379.pid"
logfile "/var/log/redis/redis-6379.log"
masterauth "123456"
requirepass 123456
appendonly yes 
```

### Start redis  

```shell
# redis-server /root/redis-5.0.14/redis.conf
```

## R2 configuration

As same as R1 

## Check replication info

```shell
# redis-cli -h 192.168.3.205
192.168.3.205:6379> auth 123456
OK
192.168.3.205:6379> info replication
# Replication
role:master
connected_slaves:2
slave0:ip=192.168.3.206,port=6379,state=online,offset=182,lag=0
slave1:ip=192.168.3.207,port=6379,state=online,offset=182,lag=0
master_replid:cc5f0546e282c4fdedb7ff554ed1d2a65053583d
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:182
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:182

# redis-cli -h 192.168.3.206
192.168.3.206:6379> auth 123456
OK
192.168.3.206:6379> info replication
# Replication
role:slave
master_host:192.168.3.205
master_port:6379
master_link_status:up
master_last_io_seconds_ago:3
master_sync_in_progress:0
slave_repl_offset:294
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:cc5f0546e282c4fdedb7ff554ed1d2a65053583d
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:294
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:294

# redis-cli -h 192.168.3.207
192.168.3.207:6379> auth 123456
OK
192.168.3.207:6379> info replication
# Replication
role:slave
master_host:192.168.3.205
master_port:6379
master_link_status:up
master_last_io_seconds_ago:1
master_sync_in_progress:0
slave_repl_offset:350
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:cc5f0546e282c4fdedb7ff554ed1d2a65053583d
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:350
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:85
repl_backlog_histlen:266

```

## S1 configuration

```shell
# Copy conf/sentinel.conf /root/redis-5.0.14/sentinel.conf
# Main keys:
sentinel monitor [master-name] [redis-ip] [redis-port] [quorum]
# 这行配置的意思是让sentinel监控某个master，如果quorum个sentinel节点同意该master不可达(不回复ping)，那么认为该master O_DOWN，准备启动failover

sentinel down-after-milliseconds mymaster 10000
# 上面配置表达的意思是如果超过10s得不到某个节点的应答，就认为该节点 S_DOWN
```

### Start sentinel

```shell
# 启动命令
sentinel /root/redis-5.0.14/sentinel.conf
```




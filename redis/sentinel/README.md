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
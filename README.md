docker-oracle-xe-11g
============================

Oracle Express Edition 11g Release 2 on Ubuntu 16.04 LTS



### Installation(with Ubuntu 16.04)
```
docker pull registry.cn-beijing.aliyuncs.com/sirius_yangchen/oracle-xe-11g-ubuntu16.04
```

Run with 22 and 1521 ports opened:
```
docker run -d -p 49160:22 -p 49161:1521 registry.cn-beijing.aliyuncs.com/sirius_yangchen/oracle-xe-11g-ubuntu16.04
```

Run this, if you want the database to be connected remotely:
```
docker run -d -p 49160:22 -p 49161:1521 -e ORACLE_ALLOW_REMOTE=true registry.cn-beijing.aliyuncs.com/sirius_yangchen/oracle-xe-11g-ubuntu16.04
```

By default, the password verification is disable(password never expired). If you want it back, run this:
```
docker run -d -p 49160:22 -p 49161:1521 -e ORACLE_PASSWORD_VERIFY=true registry.cn-beijing.aliyuncs.com/sirius_yangchen/oracle-xe-11g-ubuntu16.04
```

For performance concern, you may want to disable the disk asynch IO:
```
docker run -d -p 49160:22 -p 49161:1521 -e ORACLE_DISABLE_ASYNCH_IO=true registry.cn-beijing.aliyuncs.com/sirius_yangchen/oracle-xe-11g-ubuntu16.04
```

For XDB user, run this:
```
docker run -d -p 49160:22 -p 49161:1521 -p 8080:8080 -e ORACLE_ENABLE_XDB=true registry.cn-beijing.aliyuncs.com/sirius_yangchen/oracle-xe-11g-ubuntu16.04
```
### Start Oracle XE
Running Oracle XE in `detached` mode with `1521` and `8080` ports opened and `2GB` shared memory:

```
docker run -d --shm-size=2g -p 1521:1521 -p 8080:8080 registry.cn-beijing.aliyuncs.com/sirius_yangchen/oracle-xe-11g-ubuntu16.04
```

### Start Oracle XE and execute SQL on startup

Put your `*.sql` files for database init into some local folder and mount this folder during container startup to `/etc/entrypoint-initdb.d` volume.

```
docker run -d --shm-size=1g -p 8080:8080 -p 1521:1521 -v /local-initdb:/etc/entrypoint-initdb.d registry.cn-beijing.aliyuncs.com/sirius_yangchen/oracle-xe-11g-ubuntu16.04
```

Check if localhost:8080 work
```
curl -XGET "http://localhost:8080"
```
You will show
```
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<HTML><HEAD>
<TITLE>401 Unauthorized</TITLE>
</HEAD><BODY><H1>Unauthorized</H1>
</BODY></HTML>
```

```
# Login http://localhost:8080 with following credential:
username: XDB
password: xdb
```
Connect to Oracle Application Express web management console with following settings:
```
url: http://localhost:8080/apex
workspace: internal
user: admin
password: oracle
Do not forget to change admin password!
```
Connect database with following setting:
```
hostname: localhost
port: 49161
sid: xe
username: system
password: oracle
```

Password for SYS & SYSTEM
```
oracle
```

Login by SSH
```
ssh root@localhost -p 49160
password: admin
```

Support custom DB Initialization
```
# Dockerfile
FROM wnameless/oracle-xe-11g

ADD init.sql /docker-entrypoint-initdb.d/
```

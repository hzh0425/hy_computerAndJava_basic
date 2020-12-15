## 一 nacos

### 1.部署mysql

```
docker pull mysql:8.0
 docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:8.0
```

### 2.nacos

登录密码都是nacos

访问:http://localhost:8848/nacos

```
docker run -d \
--name nacos \
-e MODE=standalone \
-p 8848:8848 \
nacos/nacos-server
```

## 二 sentinel

```
docker run --name sentinel -d  -p 8858:8858  bladex/sentinel-dashboard
```



## 三 rabbitmq

```
docker run -d --name rabbitmq -e RABBITMQ_DEFAULT_USER=rabbitmq -e RABBITMQ_DEFAULT_PASS=rabbitmq -p 15672:15672 -p 5672:5672 rabbitmq:management
```



## 四 kafka

https://www.cnblogs.com/dalianpai/p/13789351.html



## 五 minio

```
docker run -d -p 9000:9000 --name minio \
-e "MINIO_ACCESS_KEY=minio" \
-e "MINIO_SECRET_KEY=123456789" \
-v /home/minio/data:/data \
-v /home/minio/config:/root/.minio \
minio/minio server /data
```



## 六 elasticsearch

https://blog.csdn.net/qq_32101993/article/details/100021002

1.下载镜像



```
docker pull elasticsearch:7.6.2
```

2.创建挂载的目录



```
mkdir -p /mydata/elasticsearch/config
mkdir -p /mydata/elasticsearch/data
echo "http.host: 0.0.0.0" >> /mydata/elasticsearch/config/elasticsearch.yml
```

3.创建容器并启动



```
docker run -d --name es -p 9200:9200 -p 9300:9300  -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m"    elasticsearch:7.6.2

其中elasticsearch.yml是挂载的配置文件，data是挂载的数据，plugins是es的插件，如ik，而数据挂载需要权限，需要设置data文件的权限为可读可写,需要下边的指令。
chmod -R 777 要修改的路径

-e "discovery.type=single-node" 设置为单节点
特别注意：
-e ES_JAVA_OPTS="-Xms256m -Xmx256m" \ 测试环境下，设置ES的初始内存和最大内存，否则导致过大启动不了ES
```

4.Kibana启动



```
docker pull kibana:7.6.2

docker run --name kibana -e ELASTICSEARCH_HOSTS=http://192.168.168.101:9200 -p 5601:5601 -d kibana:7.6.2


若出现service not start错误,则进入容器修改相应内容
docker exec -it kibana bash:
config/kibana.yml:

server.port: 5601
server.host: 0.0.0.0
elasticsearch.hosts: [ "http://自己的IP地址:9200" ]
i18n.locale: "Zh-CN"

然后访问页面
http://自己的IP地址:5601/app/kibana
```

## 七 seata

1、拉取镜像

```

docker pull seataio/seata-server:1.2.0
```

 

2、运行镜像

```

docker run --name seata -p 8091:8091 -d  seataio/seata-server:1.2.0
```

 

3、复制配置文件到主机

```

docker cp seata:/seata-server  /home/dockerdata/seata
```

 

4、停止服务

```

docker stop seata
```

 

5、删除服务

```

docker rm seata
```

 

6、重新运行服务，至此服务已经启动完成，接下来就是在/home/dockerdata/seata/seata-server目录中修改对应的配置（设置开机自启和关键配置挂载到本地目录方便修改配置）

```

docker run -d  --name  seata -p 8091:8091  -v /home/dockerdata/seata1.3.0:/seata-server -e SEATA_IP=192.168.168.101 -e SEATA_PORT=8091 seataio/seata-server:1.2.0 
```

 

7、切换到seata配置目录

```

cd /home/dockerdata/seata1.3.0/resources
```

 

8、修改registry.conf文件，修为nacos启用方式,绿色字体为修改的关键地方



```
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  type = "nacos"

  nacos {
    application = "seata-server"
    serverAddr = "192.168.168.101:8848"
    group = "SEATA_GROUP"
    namespace = "98ce9b4f-07de-42b0-97c8-89c986b42dd5"
    cluster = "default"
    username = ""
    password = ""
  }
  eureka {
    serviceUrl = "http://localhost:8761/eureka"
    application = "default"
    weight = "1"
  }
  redis {
    serverAddr = "localhost:6379"
    db = 0
    password = ""
    cluster = "default"
    timeout = 0
  }
  zk {
    cluster = "default"
    serverAddr = "127.0.0.1:2181"
    sessionTimeout = 6000
    connectTimeout = 2000
    username = ""
    password = ""
  }
  consul {
    cluster = "default"
    serverAddr = "127.0.0.1:8500"
  }
  etcd3 {
    cluster = "default"
    serverAddr = "http://localhost:2379"
  }
  sofa {
    serverAddr = "127.0.0.1:9603"
    application = "default"
    region = "DEFAULT_ZONE"
    datacenter = "DefaultDataCenter"
    cluster = "default"
    group = "SEATA_GROUP"
    addressWaitTime = "3000"
  }
  file {
    name = "file.conf"
  }
}

config {
  # file、nacos 、apollo、zk、consul、etcd3
  type = "file"

  nacos {
    serverAddr = "127.0.0.1:8848"
    namespace = ""
    group = "SEATA_GROUP"
    username = ""
    password = ""
  }
  consul {
    serverAddr = "127.0.0.1:8500"
  }
  apollo {
    appId = "seata-server"
    apolloMeta = "http://192.168.1.204:8801"
    namespace = "application"
  }
  zk {
    serverAddr = "127.0.0.1:2181"
    sessionTimeout = 6000
    connectTimeout = 2000
    username = ""
    password = ""
  }
  etcd3 {
    serverAddr = "http://localhost:2379"
  }
  file {
    name = "file.conf"
  }
}

```



 

项目中的库需要执行，sql文件路径：https://github.com/seata/seata/tree/develop/script/client

seata服务 需要sql https://github.com/seata/seata/tree/develop/script/server

不用的模式执行不同的脚本

具体的注意点查看/home/dockerdata/seata/README-zh.md

```
CREATE TABLE IF NOT EXISTS `undo_log`
(
    `branch_id`     BIGINT(20)   NOT NULL COMMENT 'branch transaction id',
    `xid`           VARCHAR(100) NOT NULL COMMENT 'global transaction id',
    `context`       VARCHAR(128) NOT NULL COMMENT 'undo_log context,such as serialization',
    `rollback_info` LONGBLOB     NOT NULL COMMENT 'rollback info',
    `log_status`    INT(11)      NOT NULL COMMENT '0:normal status,1:defense status',
    `log_created`   DATETIME(6)  NOT NULL COMMENT 'create datetime',
    `log_modified`  DATETIME(6)  NOT NULL COMMENT 'modify datetime',
    UNIQUE KEY `ux_undo_log` (`xid`, `branch_id`)
) ENGINE = InnoDB
  AUTO_INCREMENT = 1
  DEFAULT CHARSET = utf8 COMMENT ='AT transaction mode undo table';

CREATE TABLE IF NOT EXISTS `global_table`
(
    `xid`                       VARCHAR(128) NOT NULL,
    `transaction_id`            BIGINT,
    `status`                    TINYINT      NOT NULL,
    `application_id`            VARCHAR(32),
    `transaction_service_group` VARCHAR(32),
    `transaction_name`          VARCHAR(128),
    `timeout`                   INT,
    `begin_time`                BIGINT,
    `application_data`          VARCHAR(2000),
    `gmt_create`                DATETIME,
    `gmt_modified`              DATETIME,
    PRIMARY KEY (`xid`),
    KEY `idx_gmt_modified_status` (`gmt_modified`, `status`),
    KEY `idx_transaction_id` (`transaction_id`)
) ENGINE = INNODB
  DEFAULT CHARSET = utf8;

-- the table to store BranchSession data
CREATE TABLE IF NOT EXISTS `branch_table`
(
    `branch_id`         BIGINT       NOT NULL,
    `xid`               VARCHAR(128) NOT NULL,
    `transaction_id`    BIGINT,
    `resource_group_id` VARCHAR(32),
    `resource_id`       VARCHAR(256),
    `branch_type`       VARCHAR(8),
    `status`            TINYINT,
    `client_id`         VARCHAR(64),
    `application_data`  VARCHAR(2000),
    `gmt_create`        DATETIME(6),
    `gmt_modified`      DATETIME(6),
    PRIMARY KEY (`branch_id`),
    KEY `idx_xid` (`xid`)
) ENGINE = INNODB
  DEFAULT CHARSET = utf8;

-- the table to store lock data
CREATE TABLE IF NOT EXISTS `lock_table`
(
    `row_key`        VARCHAR(128) NOT NULL,
    `xid`            VARCHAR(96),
    `transaction_id` BIGINT,
    `branch_id`      BIGINT       NOT NULL,
    `resource_id`    VARCHAR(256),
    `table_name`     VARCHAR(32),
    `pk`             VARCHAR(36),
    `gmt_create`     DATETIME,
    `gmt_modified`   DATETIME,
    PRIMARY KEY (`row_key`),
    KEY `idx_branch_id` (`branch_id`)
) ENGINE = INNODB
  DEFAULT CHARSET = utf8;
```

9、修改存储方式为mysql

```

## transaction log store, only used in seata-server
store {
  ## store mode: file、db、redis
  mode = "db"

  ## file store property
  file {
    ## store location dir
    dir = "sessionStore"
    # branch session size , if exceeded first try compress lockkey, still exceeded throws exceptions
    maxBranchSessionSize = 16384
    # globe session size , if exceeded throws exceptions
    maxGlobalSessionSize = 512
    # file buffer size , if exceeded allocate new buffer
    fileWriteBufferCacheSize = 16384
    # when recover batch read size
    sessionReloadReadSize = 100
    # async, sync
    flushDiskMode = async
  }

  ## database store property
  db {
    ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp)/HikariDataSource(hikari) etc.
    datasource = "druid"
    ## mysql/oracle/postgresql/h2/oceanbase etc.
    dbType = "mysql"
    driverClassName = "com.mysql.jdbc.Driver"
    url = "jdbc:mysql://1921.168.168.101:3306/seata?useUnicode=true&characterEncoding=utf8&autoReconnect=true&useSSL=false&serverTimezone=Asia/Shanghai"
    user = "root"
    password = "123456"
    minConn = 5
    maxConn = 30
    globalTable = "global_table"
    branchTable = "branch_table"
    lockTable = "lock_table"
    queryLimit = 100
    maxWait = 5000
  }

  ## redis store property
  redis {
    host = "127.0.0.1"
    port = "6379"
    password = ""
    database = "0"
    minConn = 1
    maxConn = 10
    queryLimit = 100
  }

}

```

 

10、重启服务

```
docker restart seata-server
docker logs seata-server #查看启动日志
```

11、可以登录nacos查看服务列表，就会发现seata-server已经注册上来了

https://www.cnblogs.com/binz/p/12841125.html




















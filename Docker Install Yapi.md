## 1 Docker安装Yapi

## 1.1 docker安装MongoDB

- 安装

```bash
#!/bin/bash
docker stop mongo && docker pull mongo

#rm -rfv /usr/local/mongo/db
mkdir -pv /usr/local/mongo/db

docker stop mongo && docker rm mongo
docker run --name mongo --restart=always \
  -p 27017:27017 \
  --net mynet \
  -v /usr/local/mongo/db:/data/db \
  -e MONGO_INITDB_DATABASE=yapi \
  -e MONGO_INITDB_ROOT_USERNAME=yapipro \
  -e MONGO_INITDB_ROOT_PASSWORD=yapipro1024 \
  -d mongo
```

```shell
docker logs -f mongo
```

- 进入 MongoDB 容器后，进入 mongo cli

```shell
docker exec -it mongo /bin/bash
# 进入 MongoDB 容器后，进入 mongo cli
mongo localhost:27017
# 进入 MongoDB 的 mongo cli 后，执行以下语句进行初始化库表
```

![image-20221021030859760](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20221021030859760.png)

```mongodb
use admin;
db.auth("yapipro", "yapipro1024");
# 创建yapi数据库
use yapi;
# 创建给yapi使用的账号和密码，限制权限yapi是我们初始化的数据库
db.createUser({
  user: 'yapi',
  pwd: 'yapi123456',
  roles: [
 { role: "dbAdmin", db: "yapi" },
 { role: "readWrite", db: "yapi" }
  ]
});
```

![image-20221021031132532](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20221021031132532.png)

```bash
#退出Mongo Cli
exit;
#退出容器
exit
```

## 1.2 docker安装Yapi

> 到这一步你已经安装好`mongdb`了

[yapipro/yapi - Docker Image | Docker Hub](https://hub.docker.com/r/yapipro/yapi)

- 安装

```bash
docker rmi -f yapi && docker pull yapipro/yapi

docker stop yapi && docker rm yapi

BASE_DIR="/usr/local/yapi/conf"
mkdir -pv $BASE_DIR

#创建yapi配置文件,mail里面的换成自己的真实邮箱
tee $BASE_DIR/config.json <<-'EOF'
{
   "port": "3000",
   "adminAccount": "1432689025@qq.com",
   "timeout":120000,
   "db": {
     "servername": "mongo",
     "DATABASE": "yapi",
     "port": 27017,
     "user": "yapi",
     "pass": "yapi123456",
     "authSource": ""
   },
   "mail": {
     "enable": true,
     "host": "smtp.qq.com",
     "port": 465,
     "from": "*",
     "auth": {
       "user": "your_qq_email@qq.com",
       "pass": "your_password"
     }
   }
 }
EOF

# 创建容器
# 初始化管理员账号在上面的config.json配置中的: 1432689025@qq.com,  初始密码是: yapi.pro, 可以登录后进入个人中心修改
# 初始化数据库表
docker run -d --rm \
  --name yapi-init \
  --link mongodb:mongo \
  --net mynet \
  -v $BASE_DIR/config.json:/yapi/config.json \
   yapipro/yapi \
  server/install.js
 
docker stop yapi && docker rm yapi
docker run --name yapi --restart=always \
  --name yapi \
  --net mynet \
  -p 3000:3000 \
  -v $BASE_DIR/config.json:/yapi/config.json \
  -d yapipro/yapi \
  server/app.js
```

```bash
docker logs -f yapi
```

```bash
# 在服务器上验证yapi启动是否成功
curl http://127.0.0.1:3000/
```

- 初始化账号密码

user: 1432689025@qq.com

pwd: yapi.pro

# AI进化平台部署文档
## 1. 环境准备
### 1.1. 系统环境
- Ubuntu 18.04
- CPU: 8核
- 内存: 16G
- 硬盘: 1T
- 显卡: 1080以上

### 1.2. 网络
- 开放 18081端口
## 2. 安装
### 2.1. 安装依赖
#### 2.1.2. 安装mysql
```bash
sudo apt update
sudo apt install mysql-server
sudo systemctl restart mysql
# 配置mysql root 密码
sudo mysql
    SELECT user,authentication_string,plugin,host FROM mysql.user;
    ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
    FLUSH PRIVILEGES;
exit
# 建库: datasets 、powerjob-product、knowledge、rule-engine
mysql -u root -p
    CREATE DATABASE datasets DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci ;;
    CREATE DATABASE powerjob-product DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci ; ;
    CREATE DATABASE knowledge DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci ;
    CREATE DATABASE rule-engine DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
    exit
```
#### 2.1.3. 安装redis
```bash
sudo apt install redis-server
```
#### 2.1.4. 安装docker
```bash
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo apt update
apt-cache policy docker-ce
sudo apt install docker-ce
sudo systemctl status docker
```
#### 2.1.5 安装AI训练环境

### 2.2. 安装AI进化平台
#### 2.2.1. 下载AI进化平台部署包
#### 2.2.2. 解压部署包
```bashugg\\\\,,a,
cd /home
mkdir AICM
tar -zxvf aicm.tar.gz -C AICMk
```pG
#### 2.2.3. 建表
```bash
cd /home/AICM/bin
.chomd +x *.sh
./init_sql.sh
```
####  2.2.4 运行powerJob-server
```bash
# 启动powerJob-server
docker run -d \
 			 --restart=always \
       --name powerjob-server \
       -p 7700:7700 -p 10086:10086 -p 10010:10010 \
       -e TZ="Asia/Shanghai" \
       -e JVMOPTIONS="" \
       -e PARAMS="--spring.profiles.active=product --spring.datasource.core.jdbc-url=jdbc:mysql://本机ip:3306/powerjob-product?useUnicode=true&characterEncoding=UTF-8 --spring.datasource.core.username=root --spring.datasource.core.password=root" \
       -v ~/docker/powerjob-server:/root/powerjob/server -v ~/.m2:/root/.m2 \
       tjqq/powerjob-server:latest
```
#### 2.2.5.  运行nacos
```bash
cd /home/AICM/nacos/bin
bash startup.sh -m standalone
```
#### 2.2.3. 配置AI进化平台
```bash
cd /home/AICM
vim app/dataJob/application-pro.yml # 修改内容如下
        dj:
        nacos-server: ${本机ip}
vim app/gateway/application-pro.yml # 修改内容如下
        nacos:
            discovery:
                server-addr: ${本机ip}:8848
                username: nacos
                password: nacos
            config:
                server-addr: ${本机ip}:8848
                username: nacos
                password: nacos
  vim app/knowledge/application-pro.yml # 修改内容如下
        nacos:
            discovery:
                server-addr: ${本机ip}:8848
                username: nacos
                password: nacos
            config:
                server-addr: ${本机ip}:8848
                username: nacos
                password: nacos
    vim app/ruleEngine/application-pro.yml # 修改内容如下
        nacos:
            discovery:
                server-addr: ${本机ip}:8848
                username: nacos
                password: nacos
            config:
                server-addr: ${本机ip}:8848
                username: nacos
                password: nacos
         mqtt-ai-set-upload-url: http://${本机ip}:18091/data-engine/api/v1/raw-data/save
```
#### 2.2.4. 启动AI进化平台
```bash
cd /home/AICM
chmod +x app/bin/*.sh
./app/bin/startup.sh
```









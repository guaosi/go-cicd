# 手摸手教你从0到1搭建部署Go微服务-CI/CD

本仓库配合 [手摸手教你从开发到部署(CI/CD)GO微服务系列](https://www.guaosi.com/2020/07/05/go-microservice-series-from-development-to-deployment-introduction-contents/) 食用最佳噢~

## 创建专属网络

为了隔离和管理,所以先创建一个专属网络
```
docker network create cicd --driver bridge
```

## gitlab

### 更改宿主机ssh端口

把宿主机的ssh端口从22改为222(因为GIT底层也是22端口)

```
vim /etc/ssh/sshd_config
systemctl restart sshd
```

### 创建文件夹

```
mkdir -p /usr/local/data/gitlab/etc
mkdir -p /usr/local/data/gitlab/log
mkdir -p /usr/local/data/gitlab/data
```

### 启动

```
cd gitlab
docker-compose up -d
```

完全启动时间大约5分钟

## jenkins

### 创建文件夹
```
mkdir -p /usr/local/data/jenkins/data
```

### 启动
```
cd jenkins
docker-compose up -d
```
完全启动时间大约3分钟

### 查看第一次认证密码
进入容器后输入
```
cat /var/jenkins_home/secrets/initialAdminPassword
```
### 更新
```
apt-get update
```
### 给jenkins换源
```
cd /var/jenkins_home/updates

sed -i 's/http:\/\/updates.jenkins-ci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g' default.json && sed -i 's/http:\/\/www.google.com/https:\/\/www.baidu.com/g' default.json
```

### 安装gcc跟vim
```
apt install vim
apt install gcc
```

### 安装golang
```
# 下载
wget https://studygolang.com/dl/golang/go1.14.4.linux-amd64.tar.gz

# 解压
tar -C /usr/local -zxvf go1.14.4.linux-amd64.tar.gz

# 添加进环境变量
vim /etc/profile

# 在最后一行添加
export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin

# wq保存退出后source一下
source /etc/profile
```

### 安装jenkins 插件
系统管理 > 插件管理 > 可选插件
安装以下几个插件
- Localization: Chinese (Simplified)
- Publish Over SSH
- Gitlab
- Golang
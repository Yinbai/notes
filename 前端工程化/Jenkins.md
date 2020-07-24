# Jenkins

## 安装

Docker 安装：

```shell
docker pull jenkins/jenkins:lts
```

Docker 启动容器：

```shell
docker run --name devops-jenkins -d -p 8080:8080 -p 50000:50000 -v /Users/Cuptea/Documents/Application/docker/jenkins_home:/var/jenkins_home jenkins/jenkins:lts
# --name devops-jenkins 容器名字叫 devops-jenkins
# -d 后台运行
# -p 端口映射
# -v /Users/Cuptea/Documents/Application/docker/jenkins_home:/var/jenkins_home 将服务器的 jenkins_home 映射到容器的 /var/jenkins_home
```


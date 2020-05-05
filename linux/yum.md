# [yum 安装 jenkins](https://www.cnblogs.com/ruanqin/p/11090395.html)

1. 配置yum源：

   ```
   wget  -O  /etc/yum.repos.d/jenkins.repo  http://pkg.jenkins-ci.org/redhat/jenkins.repo
   ```

2. 导入rpm密钥：

   ```
   rpm  --import  https://jenkins-ci.org/redhat/jenkins-ci.org.key
   ```

3. 安装：

   ```
   yum  -y  install  jenkins  java
   ```

4. 启动服务：

   ```
   systemctl  start  jenkins
   ```

5. 访问网址：

   ```
   xx.xx.xx.xx:8080
   根据提示进行安装
   ```



如果不能安装就到官网下载jenkis的rmp包，官网地址（http://pkg.jenkins-ci.org/redhat-stable/）

```
1 wget http://pkg.jenkins-ci.org/redhat-stable/jenkins-2.7.3-1.1.noarch.rpm
2 rpm -ivh jenkins-2.7.3-1.1.noarch.rpm
```

配置jenkis的端口

```
 vi /etc/sysconfig/jenkins
```

找到修改端口号：

JENKINS_PORT="8080"  此端口不冲突可以不修改 

d7e078ce433946d6bcf1105af77d6b44
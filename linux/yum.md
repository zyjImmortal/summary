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
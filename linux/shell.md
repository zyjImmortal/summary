``````shell
#!/bin/sh
#free -h | sed -n '2p' | awk '{print "used mem is "$3"M,total mem is "$2"M,used percent is "$3/$2*100"%"}'
export PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
p1=`free -m| sed -n '2p' | awk '{print $3/$2*100}'`
echo $p1
result=`num=95 p=$p1; expr $p \> $num`
echo $result
if [[ $result -eq "1" ]]; then
echo "OUT OF MEM"
ps -ef|grep pdf.jar|grep -v *.log|grep -v grep|cut -c 9-15|xargs kill -9
cd /home && sh start.sh
else [[ $result -eq "0" ]];
echo "NORMAL"
fi
``````

enkins安装好后，会自动创建一个jenkins用户。jenkins在构建工程时，默认的权限是不够写入文件的。这时就需要把它的权限提升为root

1.将jenkins账号分别加入到root组中



```undefined
gpasswd -a root jenkins
```

2.修改/etc/sysconfig/jenkins文件中，



```bash
# user id to be invoked as (otherwise will run as root; not wise!)
JENKINS_USER="root"
JENKINS_GROUP="root"
```

可以修改为root权限运行

3.重启Jenkins



```undefined
service Jenkins restart
```

4.验证
 在Jenkins中的shell脚本中执行命令



```undefined
groups jenkins
```

查看jenkins是在哪个用户组，显示的是`root`。



作者：louhangfei
链接：https://www.jianshu.com/p/3c79fd73a923
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
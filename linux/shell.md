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

passwd 修改用户密码

Useradd 添加用户

* 文件目录

```
drwx------    5 zhouyajun  staff        160  8 23  2018 .bash_sessions
-rw-r--r--    1 zhouyajun  staff        118  6  4  2019 .bashrc
drwxr-xr-x    5 zhouyajun  staff        160  6  4  2019 .bitrise
drwxr-xr-x    3 zhouyajun  staff         96  1 16  2019 .cache
drwx------    8 zhouyajun  staff        256 12 25 15:36 .config
drwx------    3 zhouyajun  staff         96  9 13  2018 .cups
```

第一列第一个字符d表示的是目录，-横线表示的是普通文件

剩下的9个字符是权限位，每三个为一组，每一组 rwx 表示“读（read）”“写（write）”“执行（execute）”。

三个组分别表示文件所属的用户权限、文件所属的组权限以及其他用户的权限

第二列字段是硬链接（hard link）数目

第三列是所属用户，第四列是所属组。第五列是文件的大小，第六个字段是文件被修改的日期，最后是文件名
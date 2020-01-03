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


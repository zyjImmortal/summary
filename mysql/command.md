##### 创建用户并授权

```mysql
mysql 5.8版本
mysql> CREATE USER 'root'@'%' IDENTIFIED BY 'root';
# 授予用户全部权限
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;

在mysql 5.8版本
授予用户权限不能直接使用GRANT，要先使用update语句更新host为%,然后再使用grant语句授权
update user set host='%' where user='meiduo_zyj';
GRANT EXECUTE,INSERT,SELECT,UPDATE ON meiduo_mall.* TO 'meiduo_zyj'@'%';
```


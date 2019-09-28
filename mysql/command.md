<!--
 * @Description: In User Settings Edit
 * @Author: your name
 * @Date: 2019-09-14 14:46:13
 * @LastEditTime: 2019-09-14 14:46:51
 * @LastEditors: Please set LastEditors
 -->
## 创建用户
```
mysql5.8版本
mysql> CREATE USER 'root'@'%' IDENTIFIED BY 'root';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
```
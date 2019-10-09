### centos7.4安装python3.6.6环境

* 安装依赖包

  ```bash
  # yum -y groupinstall "Development tools"
  # yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
  ```

* 下载安装包

  ``````bash
  # wget https://www.python.org/ftp/python/3.6.6/Python-3.6.6.tar.xz
  ``````

* 新建python解压安装目录

  ``````
  mkdir /usr/local/python3
  ``````

* 编译安装

  ``````bash
  # tar -xvJf  Python-3.6.6.tar.xz
  # cd Python-3.6.6
  # ./configure --prefix=/usr/local/python3
  # make && make install
  ``````

* 创建软链

  ``````bash
  # ln -s /usr/local/python3/bin/python3 /usr/bin/python3
  # ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
  ``````


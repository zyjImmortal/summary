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

* https配置

  * 先从阿里云或者腾讯云获取免费证书，两个文件，上传到服务器上，

  * 然后给服务增加ssl配置

    ```upstream jenkins {
            server 127.0.0.1:9080;
    }
    ```

    server {
        listen 443 ssl;
        server_name jenkins.immortalp.com;
        charset utf-8;
       ssl_certificate /etc/nginx/ssl/cert.crt;
      ssl_certificate_key /etc/nginx/ssl/private.key;
      ssl_session_timeout 5m;
      ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #按照这个协议配置
      ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#按照这个套件配置
      ssl_prefer_server_ciphers on;

    ```

      # rewrite ^(.*) https://$host$1 permanent;
        location / {
           proxy_pass http://jenkins;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header Host $http_host;
          proxy_set_header X-Forwarded-Proto https;
          proxy_redirect off;
          proxy_connect_timeout  240;

          proxy_send_timeout   240;
          proxy_read_timeout   240;
        }
    }
    server{
      listen 80;
      server_name jenkins.immortalp.com;
      rewrite ^(.*) https://$host$1 permanent;
    }
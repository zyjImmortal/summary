### docker命令

- 启动docker服务

```shell
service docker start
```

- 查看所有容器

  ```docker ps -l```

  ``````shell
  CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                            PORTS                                                              NAMES
  4aa7333f5759        gitlab/gitlab-ce    "/assets/wrapper"   8 minutes ago       Up 8 minutes (health: starting)   0.0.0.0:222->22/tcp, 0.0.0.0:8090->80/tcp, 0.0.0.0:4443->443/tcp   gitlabce
  ``````

* 删除容器

  ```docker rm name/container-id```

* 停止容器

  ```docker stop container-name  docker kill container-name ```

* 启动容器

* 查看所有镜像

  docker image ls
  
* 删除镜像 docker image rm 镜像名/镜像ID

* docker image pull

* docker container ls 列出所有正在运行的容器

* docker container ls --all所有的容器

* docker exec -it 容器ID /bin/bash(进入容器首先执行的命令) 进入运行中的容器

* docker container stop/start/kill/rm 容器名/容器ID

* docker run  启动镜像生成容器

  - -i 
  - -t 登陆容器
  - -d 后台运行
  - -v 文件映射
  - -p 端口映射
  - --name 容器名
  
  192.168.65.3
  
  ``````dockerfile
  docker run -dti --network=host --name storage -e TRACKER_SERVER=192.168.65.3:22122 -v /Users/zhouyajun/workspace/docker-images/fastdfs/storage:/var/fdfs delron/fastdfs storage,ip写本机IP
  ``````
  
  
  
  
  
  ·
### docker命令

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

  ·
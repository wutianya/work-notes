# gcr镜像下载

```是一个在墙内悲哀的故事......```

## 一般gcr镜像下载方法

- 使用墙外服务器直接下载
- dockerhub构建
- registry代理
- ......

## 关于dockerhub创建grc镜像

1. 在github创建个repo
2. dockerhub关联github的repo
3. 在github的repo添加Dockerfile

    ```dockerfile
    FROM gcr.io/google_containers/name:tag
    ```

4. 最后dockerhub在进行构建即可

## 使用registry代理

需要台能访问gcr.io的代理服务器,使用docker-compose启动; 用registry代理方式,依赖代理服务器的网速,拉镜像的速度可能会比较慢. 但好处是不需要pull-push-pull

```yaml
version: "2"
services:
  nginx-proxy:
    image: jwilder/nginx-proxy:alpine
    container_name: nginx-proxy
    restart: always
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro
      - ./certs:/etc/nginx/certs
    ports:
      - 80:80
      - 443:443

  registry-gcr:
    image: registry:2
    container_name: registry-gcr
    restart: always
    environment:
    - REGISTRY_PROXY_REMOTEURL=https://gcr.io
    - VIRTUAL_HOST=gcr.simpletour.com

  registry-k8sgcr:
    image: registry:2
    container_name: registry-k8sgcr
    restart: always
    environment:
    - REGISTRY_PROXY_REMOTEURL=https://k8s.gcr.io
    - VIRTUAL_HOST=k8sgcr.simpletour.com
```

> 记录时间: 2019年 03月 22日 星期五 11:49:14 CST

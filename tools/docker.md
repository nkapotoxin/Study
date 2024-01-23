## docker

### 1 Start A Web Server With Nginx

#### 1.1 Search image

    docker search nginx
    docker pull ngix
    docker images nginx

#### 1.2 Start nginx
    docker run --rm --name gb_wa -p 8080:80 -d nginx
    --rm: 容器终止运行后，自动删除容器文件
    --name: 指定容器名字
    -p: 设置容器端口映射
    -d: 容器启动后，在后台运行

#### 1.3 Visit nginx
    mkdir -p /home/nginx/www /home/nginx/logs /home/nginx/conf
    docker cp <docker-id>:/etc/nginx/nginx.conf /home/nginx/conf/
    docker run --rm --name gb_wa -p 8080:80 -d -v /home/nginx/www:/usr/share/nginx/html -v
    /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v /home/nginx/logs:/var/log/nginx nginx

    cd /home/nginx/www/
    vim index.html
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>Nginx test !!!</title>
    </head>
    <body>
     <h1>我的第一个标题</h1>
     <p>我的第一个段落。</p>
    </body>
    </html>

#### 1.4 Test command
    docker run alpine/curl sh -c "while true; do curl -s http://192.168.0.146:9300/get?key=m1; echo&sleep 1; done"
    docker run alpine/curl sh -c "for i in 1 2 3 4 5 6; do curl -s http://192.168.0.146:9300/get?key=m1; sleep 1; done"

#### 1.5 Docker buildx
    ```
    # 启用docker buildx
    DOCKER_BUILDKIT=1 docker build --platform=local -o . "https://github.com/docker/buildx.git"
    mkdir -p ~/.docker/cli-plugins
    mv buildx ~/.docker/cli-plugins/docker-buildx
    ```

    ```
    # 新建builder实例
    docker run --rm --privileged tonistiigi/binfmt:latest --install all
    docker buildx create --use --name=mybuilder --driver docker-container --driver-opt image=dockerpracticesig/buildkit:master
    docker buildx use mybuilder
    ```

    ```
    # 启动构建
    docker buildx build --platform linux/arm,linux/arm64,linux/amd64 -t nkapotoxin/dds:3.0 . --push
    ```




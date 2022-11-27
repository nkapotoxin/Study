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



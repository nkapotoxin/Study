## AWK

### 删除指定容器

    docker ps -a | grep curl | awk '{print $1}' | xargs docker rm



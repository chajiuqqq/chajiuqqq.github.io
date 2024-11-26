
![image](https://github.com/user-attachments/assets/e7bfc382-8259-4b9c-8e9c-b0f973d4d74b)

[PicImpact](https://github.com/besscroft/PicImpact)是一个摄影师专用的摄影作品展示网站，特点是瀑布流照片展示和支持 AWS S3 API、Cloudflare R2、AList API 的外部存储，不用再为难阿里云99的小鸡了🤣。上传还自动会生成压缩预览图，加快访问速度，很赞，下面是配置过程。[我的demo](https://photo.chajiuqqq.cn/)

# 配置过程
1. `docker-compose.yml`把picimpact和postgres启动
```yml
version: '3.9'
services:
  mypostgres:
    image: postgres:latest
    container_name: postgres-for-photos
    environment:
      POSTGRES_DB: myphotos
      POSTGRES_PASSWORD: pw123
    restart: always
    volumes:
      - ./data:/var/lib/postgresql/data

  picimpact:
    image: besscroft/picimpact:latest
    container_name: picimpact
    depends_on:
      - mypostgres
    ports:
      - "9001:3000"
    environment:
      DATABASE_URL: postgresql://postgres:pw123@mypostgres:5432/myphotos
      AUTH_SECRET: "123456577888"
    restart: always
```

2. 由于我的alist在家里的NAS上，且无公网IP，所以考虑了tailscale+caddy进行代理转发实现图片公网访问，如果你的alist部署在公网ip的机器上，不用这一步：
```
photo.chajiuqqq.cn {
    reverse_proxy localhost:9001
}

alist.chajiuqqq.cn {
    reverse_proxy 100.127.185.26:5244
}
```
3. picimpact的alist配置：

![image](https://github.com/user-attachments/assets/4feca019-e8f5-4640-8677-7e9d476c34aa)

令牌在alist的其他配置中找到：
![image](https://github.com/user-attachments/assets/ff8dd5ab-98b4-4827-a687-3764baecd6a7)

> tips：通过picimpact上传的照片地址是由配置的alist地址关联的，所以上一步用caddy做了一个反向代理

上传完照片就存到了指定的alist下，而alist可以配置挂载到网盘或者本地存储，很方便：
![image](https://github.com/user-attachments/assets/9e074016-f8b9-4aaf-8560-7f8641c44ac5)

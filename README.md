# 50IPCheck

## 项目简介

一个简单的纯前端项目，可以方便的调用Wikihost提供的IP状态查询API，方便地获取您所查询IP在境内/境外的访问性。

效果预览：

![](https://user-images.githubusercontent.com/49261063/110353848-7e635c00-8072-11eb-9234-1b37663941e3.png)

## 项目部署

### 使用预构建代码

你可以直接从Release中下载我已经构建好的项目文件。在这种情况下，你只需要像配置一个静态网页一样配置NGINX即可。

```nginx
server {
    listen 80;
    server_name example.com;
    location / {
        root /yourpath/dist;
        index index.html
    }
}
```

### 自行构建

您也可以自行构建，或者在此基础上二次开发。

获取源码：

```shell
git clone https://github.com/DDCHlsq/50IPCheck.git
```

进入目录：

```shell
cd 50IPCheck
```

安装依赖：

```shell
npm install
```

测试运行：

```shell
npm run serve
```

构建：

```shell
npm run build
```




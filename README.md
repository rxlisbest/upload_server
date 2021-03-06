# php-upload-server

#### 项目要求
- linux
- php >= 7.1
- swoole
- mysql
- ffmpeg

#### 部署步骤
- git clone https://github.com/rxlisbest/php-upload-server.git
- 新建数据库upload_server
- 修改config/database.php
```
    // 服务器地址
    'hostname'        => '127.0.0.1',
    // 数据库名
    'database'        => 'upload_server',
    // 用户名
    'username'        => 'root',
    // 密码
    'password'        => 'root',
```
- 修改config/upload.php
```
    return [
        // dir
        'dir' => '/Library/WebServer/Documents/htdocs/upload_server/public/upload/', // 上传文件的根目录
    ];
```
- 修改config/ffmpeg.php(ffmpeg程序绝对路径)
```
return [
    // dir
    'bin' => '/usr/local/bin/ffmpeg', // ffmpeg bin file
];
```
- 项目根目录执行命令 php think migrate:run
- 项目根目录执行命令 php think seed:run
- 项目根目录执行命令 composer update
- 将虚拟目录配置到public文件夹下
```
server {
    listen       80;
    server_name  www.qituzi.com;

    client_max_body_size 8M;
    client_body_buffer_size 8M;

    location / {
        root   /Library/WebServer/Documents/htdocs/upload_server/public;
        # root   html;
        index  index.html index.htm index.php;
        if (!-e $request_filename) {
            rewrite  ^(.*)$  /index.php?s=/$1  last;
        }
    }
    
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }
    
    location ~ \.php$ {
        root           /Library/WebServer/Documents/htdocs/upload_server/public;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```
- 项目根目录执行命令 php public/index.php index/Index/process（启动转码队列）

#### 说明
- 文件存储规则/[config/upload.php中配置的上传根目录]/[用户ID]/[bucket]/文件名
- 此项目可以与 https://github.com/rxlisbest/dynamic-domain 配合使用，来实现通过域名和文件名访问上传的资源

#### 访问地址
- 上传：http://域名
- 后台管理：http://域名/admin
- 前台上传example：http://域名/examples/index

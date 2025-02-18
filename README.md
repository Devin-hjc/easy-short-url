# easy-short-url 短网址生成

- 实现原理: id 自增（转自定义62进制）  
- 存储: mysql  
- 统计: 302 重定向, 数据库 request_num 字段统计（如考虑性能，可自己在 index.php 修改为 301 永久重定向，统计将失效）  

## 使用步骤

1.获取包
```
composer require chenlongqiang/easy-short-url
```

2.创建数据库
```
mysql -u username -ppassword
create database esu character set utf8 collate utf8_general_ci;
```

3.创建数据表
```
mysql -u username -ppassword esu < esu.sql
```

4.在项目根目录下，创建配置文件 .env
```
cd 你的项目根目录
cp ./vendor/chenlongqiang/easy-short-url/.env_example ./.env
```

5.vi .env 修改配置项
```
//生成的短网址域名
DOMAIN=http://s.lukachen.com
//web 页 session 有效时间
WEB_SESSION_LIFE=600
//api ACCESS_KEY
ACCESS_KEY=easy123456|short099876|url123567

DB_HOST=127.0.0.1
DB_DBNAME=esu
DB_USERNAME=root
DB_PASSWORD=root

TABLE_URL=esu_url
```

## 方法列表

0.config
```
$dbConfig = [
    'host' => env('DB_HOST'),
    'dbname' => env('DB_DBNAME'),
    'username' => env('DB_USERNAME'),
    'password' => env('DB_PASSWORD'),
];
$options = [
    'domain' => env('DOMAIN'),
    'tableUrl' => env('TABLE_URL'),
];
```

1.生成短网址 toShort
```
$shortUrl = \EasyShortUrl\EasyShortUrl::getInstance($dbConfig, $options)->toShort('http://lukachen.com/archives/328/');
```

2.获取原网址 toLong
```
$longUrl = \EasyShortUrl\EasyShortUrl::getInstance($dbConfig, $options)->toLong($code);
```

完成以上步骤，即可在项目中引入本包，toShort、toLong 完成长短链接转化。  
如果不需要配置独立的转链网站，后面就不用看了 :)  

## 需要搭建转链网站

需搭建类似 http://s.lukachen.com/web_admin 这样的网站，继续以下步骤（本项目已经提供前端页面，做好域名和服务器配置即可）  

1.服务器配置
```
1) apache or nginx 配置 root 目录至 vendor/chenlongqiang/easy-short-url/
2) 配置 rewrite 重写至index.php，不清楚的自行baidu、google或联系我
```Nginx重写index.php
    location / {
            if (!-e $request_filename){
                rewrite ^/(.*)$ /index.php/$1 last;
            }
            index  index.html index.htm index.php;
            #autoindex  on;
        }

2.web页（.env DOMAIN 改成自己的域名）
```
地址: http://(你的短网址域名)/web_admin
授权: web页自带 session_key 授权，session_key 有效期在 .env 中配置 WEB_SESSION_LIFE 单位秒
```

3.api
```
地址: http://(你的短网址域名)/api_gen
方法: POST
参数:
    type: to_short 或 to_long
    content: url
    access_key: api 授权 key 在 .env 中新增，多个 ACCESS_KEY 使用 | 分割
```

## 作者
- QQ 365499684 (添加时备注【短网址】)
- Blog http://lukachen.com
- 短网址 http://s.lukachen.com/web_admin
- 觉得对你有所帮助，请点个 star 谢谢 :)

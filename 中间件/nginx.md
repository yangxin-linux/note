### 1. nginx基础配置

#### 1.1 默认配置

```bash
# 和服务器CPU内核数相对应
worker_processes  1; 

events {
	# 支持客户端的最大连接数
    worker_connections  1024; 
}

http {
	# 导入其他的配置文件,避免配置信息全写在主配置文件导致杂乱无章
    include       mime.types;
    
    # 如果 mime.types 不包含返回的数据类型就采用默认类型: application/octet-stream
    default_type  application/octet-stream; 
	
	# 直接通过服务器网络接口发送数据给客户端,而不是使用nginx读取数据然后转发给客户端,加快传输过程
    sendfile        on;
	
	# 保持长连接的时间
    keepalive_timeout  65;

	# 虚拟主机 vhost
    server {
    	# listen 监听的端口号,多个虚拟主机不能监听相同的端口号
        listen       80;
        # 域名、主机名
        server_name  localhost;

		# 主机子目录, http://hxh/wiki/xxxx
		# location /xxx 匹配域名后面的内容
        location / {
        	# 寻找资源的位置
        	# root 表示在nginx目录下 有一个html目录
            root   html;
            # 在上一行配置资源的的目录默认页是文件是: index.html 和 index.htm
            index  index.html index.htm;
        }
		
		# 出现错误 500 502 503 504 会跳转到 htpp://hxh.wiki/50x.html
        error_page   500 502 503 504  /50x.html;
        # htpp://hxh.wiki/50x.html 这个资源默认是没有的
        
        location = /50x.html {
       	# 配置当用户访问 htpp://hxh.wiki/50x.html 去nginx目录下的html目录寻找对应的资源50x.html
            root   html;
        }  
    }
}
```



#### 1.2.多个虚拟主机配置

##### 1.2.1不同端口号

```bash
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;

    keepalive_timeout  65;

    server {

        listen       80;
        server_name  localhost;

        location / {
            root   html;
            index  index.html index.htm;
        }
        
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }      
    }
    
    server {

        listen       88;
        server_name  hxh.wiki;

        location / {
        	# 访问 http://hxh.wiki ,在nginx目录下的 www 目录里寻找对应资源
            root   www;
            index  index.html
        }
        
    }

}
```



##### 1.2.2 相同端口号

```bash
server {
		listen       80;
        server_name  hxh.wiki;
        # location目录后加 "/", 只能匹配目录，不加 “/” 不仅可以匹配目录还对目录进行模糊匹配
        # location  /dev/ 只能匹配 hxh.wiki/dev/ 的请求
        # location  /dev/ 能匹配 hxh.wiki/dev1/xx  hxh.wiki/dev2xx
        location  /dev {
        
        		# proxy_pass 末尾不加 /
        		#proxy_pass的目标地址，默认不带/，表示只代理域名，url和querystring部分不会变
        		# 浏览器访问 hxh.wiki/dev/a.html 代理后实际请求地址是 : http://43.128.111.99:8080/dev/a.html
                proxy_pass http://43.128.111.99:8080;
        }
        
        location  /test1/ {
       			# 目录后面不加(product)   /
                # 浏览器访问 hxh.wiki/test1/a.html 代理后实际请求地址是 : http://43.128.111.99:8081/producta.html
                proxy_pass http://45.127.16.54:8081:8081/product;
                           
        }
        
        location  /test2/ {
       			# 目录后面加上(product/)   /
                # 浏览器访问 hxh.wiki/test2/a.html 代理后实际请求地址是 : http://43.128.111.99:8081/product/a.html
                proxy_pass http://45.127.16.54:8081:8081/product/;
                           
        }

       location  /prod {
       			# proxy_pass 末尾加上 /
                # 如果在目标地址后增加/，则表示把path中location匹配成功的部分剪切掉之后再拼接到proxy_pass目标地址
                # 浏览器访问 hxh.wiki/dev/a.html 代理后实际请求地址是 : http://43.128.111.99:8081/a.html
                proxy_pass http://45.127.16.54:8081:8081/;
        }
}
```

```bash
server {
  listen 80;
  server_name localhost;
  
  # http://localhost/wddd01/xxx -> http://localhost:8080/wddd01/xxx
  location /wddd01/ {
    proxy_pass http://localhost:8080;
  }
 
  # http://localhost/wddd02/xxx -> http://localhost:8080/xxx
  location /wddd02/ {
    proxy_pass http://localhost:8080/;
  }
 
  # http://localhost/wddd03/xxx -> http://localhost:8080/wddd03*/xxx
  location /wddd03 {
    proxy_pass http://localhost:8080;
  }
 
  # http://localhost/wddd04/xxx -> http://localhost:8080//xxx，请注意这里的双斜线，好好分析一下。
  location /wddd04 {
    proxy_pass http://localhost:8080/;
  }
 
  # http://localhost/wddd05/xxx -> http://localhost:8080/hahaxxx，请注意这里的haha和xxx之间没有斜杠，分析一下原因。
  location /wddd05/ {
    proxy_pass http://localhost:8080/haha;
  }
 
  # http://localhost/api6/xxx -> http://localhost:8080/haha/xxx
  location /wddd06/ {
    proxy_pass http://localhost:8080/haha/;
  }
 
  # http://localhost/wddd07/xxx -> http://localhost:8080/haha/xxx
  location /wddd07 {
    proxy_pass http://localhost:8080/haha;
  }
        
  # http://localhost/wddd08/xxx -> http://localhost:8080/haha//xxx，请注意这里的双斜杠。
  location /wddd08 {
    proxy_pass http://localhost:8080/haha/;
  }
}
```

```bash
worker_processes  1;

events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;

    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;

        location / {
            root   html;
            index  index.html index.htm;
        }   
        
        location /test/ {
        	# 8081 对应tomcat服务器
        	# proxy_pass 需要加上  /   hxh.wiki/test/:80  >>  hxh.wiki:8081
            # 如果不加 /  hxh.wiki/test/:80  >>  hxh.wiki/test/:8081  会导致资源 404
            proxy_pass http://127.0.0.1:8081/;
        }
        
        location /dev/ {
        	# 访问 http://hxh.wiki/dev:80  , 转发给本地 8082 端口
            proxy_pass http://127.0.0.1:8082/;
        }
		
		
        location ~ /alist {
            proxy_pass http://127.0.0.1:5244;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header Host $http_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header Range $http_range;
            proxy_set_header If-Range $http_if_range;
            proxy_redirect off;
            client_max_body_size 20000m;
        }
        
        
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }       
    }

}

```



#### 1.3. server_name多种匹配方式

```bash
server {

        listen       80;
        # 多个域名匹配同一个站点, 域名之间用空格
        # server_name  email.hxh.wiki blog.hxh.wiki;
        
        # 除了 www.hxh.wiki这个域名 其他任意 aa.hxh.wiki 或者 bb.hxh.wiki 都到此站点
        # server_name  *.hxh.wiki;
        
        # 正则匹配任意数字开头都访问此站点  13.hxh.wiki 
        server_name  ~^[0-9]+\.hxh\.wiki;

        location / {
            root   html;
            index  index.html index.htm;
        }
        
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }      
    }
```



### 2.反向代理、负载均衡

#### 2.1 反向代理基本配置

```bash
server {

        listen       80;  
        server_name  localhost;

        location / {
            root   html;
            index  index.html index.htm;
        }
        
        location /ql/ {
            proxy_pass http://43.128.111.99:5777/;
        }
        
        location /baidu/ {
            proxy_pass http://www.bilibili.com/;
        }
        
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }      
    }
```



#### 2.2 负载均衡基本配置

```bash
worker_processes  1;

events {
    worker_connections  1024;
}



http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;

    keepalive_timeout  65;

	# 定义一组功能相同的服务器,服务器集群 名称任意,但是保持唯一
	# 负载均衡默认策略 轮询
    upstream httpds{
        server 127.0.0.1:8080;
        server 127.0.0.1:8081;
        server 127.0.0.1:5244;
    }

    server {
        listen       80;
        server_name  localhost;
        
# 多个正则location直接按书写顺序匹配，成功后就不会继续往后面匹配
# 普通（非正则）location会一直往下，直到找到匹配度最高的（最大前缀匹配）
# 当普通location与正则location同时存在，如果正则匹配成功,则不会再执行普通匹配
# 所有类型location存在时，“=”匹配 > “^~”匹配 > 正则匹配 > 普通（最大前缀匹配）

        location / {
            root   html;
            index  index.html index.htm;
        }
        
        location /apps/ {
        	# httpd 是上面 upstream 定义的一组服务器别名
            proxy_pass http://httpds;
        }
		
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }       
    }

}

```

##### 2.2.1 负载均衡策略

```bash
# 有关键字 weoght 表示权重,根据每台服务器性调整其承载请求能力
upstream httpds{
    server 127.0.0.1:8080 weoght=7;
    server 127.0.0.1:8081 weoght=2;
    server 127.0.0.1:5244 weoght=1;
}

#------------------------------------

# 有关键字 down 的服务器不会参与负载均衡,休息一会
upstream httpds{
    server 127.0.0.1:8080 weoght=7;
    server 127.0.0.1:8081 weoght=2 down;
    server 127.0.0.1:5244 weoght=1 down;
}

#------------------------------------

# 有关键字 backup 的服务器只有这一组其他的服务器都不起作用,才会启用这台备用服务器
upstream httpds{
    server 127.0.0.1:8080 weoght=7;
    server 127.0.0.1:8081 weoght=2;
    server 127.0.0.1:5244 weoght=1 backup;
}

#------------------------------------

ip_hash
# 单独的轮询无法保证会话一致性,比如上一台服务器用户保存的cookie,下一台服务器可能就没有
# ip_hash  对IP进行hash计算 相同的IP指向相同的服务器
# ip_hash 缺点就是 当用户在有线网络,ip相对固定的情况下起作用，但是当用户是移动网络,不断地变化基站时,ip_hash是不起作用的

# 对最少请求连接数进行请求,不建议使用
least_conn

# 根据服务器响应的时间调整负载均衡策略,也不建议使用，因为可能有流量倾斜,需要下载相应的插件
fair

# 定向流量转发,固定资源不在同一个服务器建议使用,但是需要保证会话的连接不建议使用
url_hash

```



### 3. 动静分离

#### 3.1 基础配置

```bash
server {

        listen       80;
        
        server_name  localhost;
		
		# 将项目用到的所有静态资源目录如css、js、images移动到 nginx的 html目录下
        location / {
        	proxy_pass http://127.0.0.1:8080
        }
        
        #location /css {
        #    root   html;
        #    index  index.html index.htm;
        #}
        
        #location /js {
        #    root   html;
        #    index  index.html index.htm;
        #}
        #
        #location /images {
        #    root   html;
        #    index  index.html index.htm;
        #}
        
        # 使用正则表达式 可以同时匹配 css、js、images多个路径
        # ~ 表示使用正则 * 忽略大小写  | 或者
        # = 精准匹配 /通用匹配,任何请求都可以匹配到
        location ~* /(css|js|images){
        	root   html;
            index  index.html index.htm;
        }
        
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }      
}

```

#### 3.2 伪静态配置

```bash
location / {

# rewrite  <regex>  <replacement> [flag];
# 关键字      正则       替代内容    flag标记
# flag标记说明：
# last#本条规则匹配完成后，继续向下匹配新的location URI规则
# break#本条规则匹配完成即终止，不再匹配后面的任何规则
# redirect#返回302临时重定向，浏览器地址会显示跳转后的URL地址
# permanent#返回301永久重定向，浏览器地址栏会显示跳转后的URL地址
	  
	  
	  # 访问 http://hxh.wiki/22.html 经过nginx后地址会被重写成 /index.jsp?pageNum=22
	  rewrite ^[0-9].html /index.jsp?pageNum=$1 break;
      proxy_pass http://127.0.0.1:8080
      
}
```



### 4. 防盗链

```bash
# none， 检测 Referer 头域不存在的情况。
# blocked，检测 Referer 头域的值被防火墙或者代理服务器删除或伪装的情况。这种情况该头域的值不以 “http://” 或 “https://” 开头
# server_names ，设置一个或多个 URL ，检测 Referer 头域的值是否是这些 URL 中的某一个	
	
	location = ~/(js|css|images) {
	
	# 检测资源的请求IP的来源是否是 hxh.wiki 或者 ss.wiki
    valid_referers hxh.wiki ss.wiki;
    if ($invalid_referer) {
    	# 如果校验错误,返回状态码401
    	return 401;
    }
}
		# 配置401禁止访问的错误页面
		error_page  401  /401.html;
        location = /401.html {
            root   html;
        }      
```



### 5. 高可用配置

```bash
#TODO
```



### 6. CA证书

```bash
# 基于腾讯云服务器配置
# 服务器运营商申请证书文件后,将其下载本地然后上传到服务器
# 解压后得到几个文件,我们只需要将 .crt 和 .key 结尾的两个文件上传到  nginx/conf/  目录下就行,默认的配置文件都会在此目录下查找

server {
	listen 443 ssl;
	server_name localhost;
	
	ssl_certificate        xtools.wiki_bundle.crt;
	ssl_certificate_key    xtools.wiki.key;
	
	location /test/ {
		proxy_pass https://127.0.0.1:7878;
	}
  }	

# 测试 https://xtools.wiki
```



### 7. 快速搭建 discuz 论坛

```bash
# 1.执行 LNMP(linux、nginx、mysql、php) 安装脚本
wget -c http://mirrors.linuxeye.com/oneinstack-full.tar.gz && tar xzf oneinstack-full.tar.gz && ./oneinstack/install.sh --nginx_option 1 --php_option 9 --phpcache_option 1 --db_option 2 --dbinstallmethod 1 --dbrootpwd qwer1234 --reboot

# 2.下载 discuz 压缩包 然后解压,重命名源码目录(upload)
wget https://gitee.com/Discuz/DiscuzX/attach_files/1345207/download
unzip -d ./discuz download
cd discuz && mv upload bbs

# 3. 将解压后的目录移动到 /opt/nginx/html 目录下 根据个人nginx安装目录不同 路径前缀也不一样
mv bbs /opt/nginx/html/

# 4. 配置 nginx.conf
cd /opt/nginx/conf
vim nginx.conf

#---------------------------------------------------------------------
user www www;
worker_processes auto;

error_log /data/wwwlogs/error_nginx.log crit;
pid /var/run/nginx.pid;
worker_rlimit_nofile 51200;

events {
  use epoll;
  worker_connections 51200;
  multi_accept on;
}

http {
  include mime.types;
  default_type application/octet-stream;
  server_names_hash_bucket_size 128;
  client_header_buffer_size 32k;
  large_client_header_buffers 4 32k;
  client_max_body_size 1024m;
  client_body_buffer_size 10m;
  sendfile on;
  tcp_nopush on;
  keepalive_timeout 120;
  server_tokens off;
  tcp_nodelay on;

  fastcgi_connect_timeout 300;
  fastcgi_send_timeout 300;
  fastcgi_read_timeout 300;
  fastcgi_buffer_size 64k;
  fastcgi_buffers 4 64k;
  fastcgi_busy_buffers_size 128k;
  fastcgi_temp_file_write_size 128k;
  fastcgi_intercept_errors on;

  #Gzip Compression
  gzip on;
  gzip_buffers 16 8k;
  gzip_comp_level 6;
  gzip_http_version 1.1;
  gzip_min_length 256;
  gzip_proxied any;
  gzip_vary on;
  gzip_types
    text/xml application/xml application/atom+xml application/rss+xml application/xhtml+xml image/svg+xml
    text/javascript application/javascript application/x-javascript
    text/x-json application/json application/x-web-app-manifest+json
    text/css text/plain text/x-component
    font/opentype application/x-font-ttf application/vnd.ms-fontobject
    image/x-icon;
  gzip_disable "MSIE [1-6]\.(?!.*SV1)";

  ##Brotli Compression
  #brotli on;
  #brotli_comp_level 6;
  #brotli_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript application/javascript image/svg+xml;

  ##If you have a lot of static files to serve through Nginx then caching of the files' metadata (not the actual files' contents) can save some latency.
  #open_file_cache max=1000 inactive=20s;
  #open_file_cache_valid 30s;
  #open_file_cache_min_uses 2;
  #open_file_cache_errors on;

  log_format json escape=json '{"@timestamp":"$time_iso8601",'
                      '"server_addr":"$server_addr",'
                      '"remote_addr":"$remote_addr",'
                      '"scheme":"$scheme",'
                      '"request_method":"$request_method",'
                      '"request_uri": "$request_uri",'
                      '"request_length": "$request_length",'
                      '"uri": "$uri", '
                      '"request_time":$request_time,'
                      '"body_bytes_sent":$body_bytes_sent,'
                      '"bytes_sent":$bytes_sent,'
                      '"status":"$status",'
                      '"upstream_time":"$upstream_response_time",'
                      '"upstream_host":"$upstream_addr",'
                      '"upstream_status":"$upstream_status",'
                      '"host":"$host",'
                      '"http_referer":"$http_referer",'
                      '"http_user_agent":"$http_user_agent"'
                      '}';

######################## default ############################
server {
        listen 443 ssl;
        server_name localhost;
	
	root html;
    	index index.html index.htm index.php;
    	#error_page 404 /404.html;
    	#error_page 502 /502.html;
	access_log /data/wwwlogs/access_nginx.log combined;

        ssl_certificate        xtools.wiki_bundle.crt;
        ssl_certificate_key    xtools.wiki.key;

        location /test/ {
                proxy_pass https://127.0.0.1:7878;
        }

	
	location /nginx_status {
      stub_status on;
      access_log off;
      allow 127.0.0.1;
      deny all;
    }
    location ~ [^/]\.php(/|$) {
      #fastcgi_pass remote_php_ip:9000;
      fastcgi_pass unix:/dev/shm/php-cgi.sock;
      fastcgi_index index.php;
      include fastcgi.conf;
    }
    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|flv|mp4|ico)$ {
      expires 30d;
      access_log off;
    }
    location ~ .*\.(js|css)?$ {
      expires 7d;
      access_log off;
    }
    location ~ ^/(\.user.ini|\.ht|\.git|\.svn|\.project|LICENSE|README.md) {
      deny all;
    }
    location /.well-known {
      allow all;
    }

	location ~ /alist {
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header Host $http_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header Range $http_range;
            proxy_set_header If-Range $http_if_range;
            proxy_redirect off;
            proxy_pass https://127.0.0.1:5244;
            client_max_body_size 20000m;
        }
   }  

	server {
    	listen 80;
    	server_name xtools.wiki;
		
		# 将所有的http请求重定向到https
		return 301 https://$server_name$request_uri;
  }
########################## vhost #############################
  include vhost/*.conf;
}

#---------------------------------------------------------------------

# 5.访问 https://xtools.wiki/bbs/install
# 提示一些文件权限不够, 测试环境下 我们可以将提示文件权限全部修改成 777  注意:生产环境别这样搞
chmod -R 777 bbs/

# 6. 后面步骤非常简单 直接按提示一步步操作即可
```



### 8.others

#### 8.1配置说明

```bash
# 指执行定 worker进程的用户
#user  nobody;

# worker_processes  1;
# auto 指令表示自动判断cpu个数,与cpu个数相同,lscpu | grep "CPU(s):"
worker_processes  auto;

# 进程优先级
worker_priority -5;

# 总并发量最大值
worker_rlimit_nofile 65536;

# 设置日志存放位置
# error_log指令 可以在server块中,为每个虚拟主机创建日志  error_log log_path log_level ;
# error_log /apps/nginx/logs/pc/error.log error ;
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

pid /apps/nginx/run/nginx.pid;


events {
    # 设置单个进程最大并发连接数,此值一般建议设置大一些
    # work_connections * cpu个数 <= worker_rlimit_nofile
    # ulimit -n 程序最大打开文件个数设置 echo "*  -   nofile   65536" >> /etc/security/limits.conf
    worker_connections  1024;

    # 设置事件模型 linux建议epoll，FreeBSD建议采用kqueue，window下不指定
    use epoll;

    # 避免惊群现象
    accept_mutex on;

    # 设置每个进程可以同时接受多个网络请求
    multi_accept on;

}


http {
    # 默认支持的文件格式,cat /apps/nginx/conf/mime.types
    include       mime.types;

    # 如果文件类型不属于默认类型的任何一种,就当成二进制文件,访问后直接下载
    default_type  application/octet-stream;

    charset utf-8;
    server_tokens off;


    sendfile        on;

    # 长链接超时时间,单位s,65是浏览器获取到的时间,30是真实时间
    keepalive_timeout  65 30;
    # 保持连接可提供的最大请求数。请求次数达到上限后，连接将关闭
    keepalive_requests 100;


    # 引入其他的配置文件,文件引入的先后,文件名默认排序都会影响到最后访问效果
    #include /apps/nginx/conf.d/*.conf;

    server {
        listen       80;
        server_name  localhost;

        # 只允许局域网主机访问
        # 要主要配置前后次序,类似iptables,匹配不到继续匹配,匹配到了直接返回
        allow 192.168.10.0/24;
        deny all;

        root  /data/nginx/static ;
        # 默认家目录,其它location没有设置时,就用默认的
        index  index.html index.htm;


        # 访问以(jpg|png...)的文件,优先取 /data/nginx/static 目录下查找
        #location ~* \.(jpg|png|gif|css|js|html|tif|tiff|icon|wmf|bmp|txt|sh|py) {

         #   alias /data/nginx/static ;

        #}

        location / {
            root   html ;
            index  index.html index.htm;

        }

        location /status {
            # 开启状态页,需要编译对应的插件
            stub_status ;

            #Actice connections：#当前处于活动状态的客户端连接数，包括等待空闲连接数=reading+writing+waiting
            #accepts：统计总值，Nginx自启动后己经接受的客户端请求的总数。
            #handled：Nginx自启动后已经处理完成的客户端请求总数除非有因worker_connections限制等被拒绝的连接
            #requests：#统计总值，Nginx自启动后客户端发来的总的请求数。
            #Reading：#当前状态，正在读取客户端请求报文首部的连接的连接数，数值越大，说明排队现像严重，性能不足
            #writing：#当前状态，正在向客户端发送响应报文过程中的连接数，数值越大，说明访问量很大
            #waiting：#当前状态，正在等待客户端发出请求的空闲连接数，开启 keep-alive的情况下，这个值等于 active（reading+wr iting）
            allow 192.168.10.201;
            deny all;
        }

        location /admin {

            # yum -y install httpd-tools  # htpasswd软件包
            # htpasswd -cb /apps/nginx/conf/.htpasswd hxh 123456 # 记录用户密码认证的文件,-b 非交互式提交密码,-c如果没有,创建该文件
            auth_basic           "登录提示信息";
            auth_basic_user_file  /apps/nginx/conf/.htpasswd;

            alias /data/nginx/static ;
        }

        location /download {

            auth_basic           "登录提示信息";
            auth_basic_user_file  /apps/nginx/conf/.htpasswd;

            # 自动索引功能
            autoindex on;

            # 计算文件确切大小,默认off,不计算
            autoindex_exact_size on;

            # 显示本机时间
            autoindex_localtime on;

            # 限速,默认不限速
            limit_rate 1024k;

            alias /data/nginx/share;

        }


        # = 用于标准uri前，需要请求字串与uri精确匹配，大小敏感，如果匹配成功就停止向下匹配并立即处理请求
        # ^~  用于标准uri前，表示包含正则表达式，并且匹配以指定的正则表达式开头，对URI的最左边部分做匹配检查，不区分字符大小写
        # ~  用于标准uri前，表示包合正则表达式，并且区分大小写
        #  ~* 用于标准uri前，表示包含正则表达式，并且不区分大写
        # 不带符号 匹配起始于此uri的所有的url
        #  \  用于标准uri前，表示包含正则表达式并且转义字符。可以将．* ？等转义为普通符号
        # 匹配优先级    =       ^~  ~ /  ~*  不带符号

        #localtion /temp {
            # root 定义资源家目录,如访问 127.0.0.1/temp
            # 会去找  /data/nginx/html/temp/index.html 这个页面,即 root只定义家目录
            #root /data/nginx/html;

            # alias 定义别名,如访问 127.0.0.1/temp
            # 会去找  /data/nginx/html/index.html 这个页面
        #    alias /data/nginx/html;

        #}


        # 404 临时重定向到主页 =302 不能有空格
        error_page  404 =302             /index.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```

#### 8.2.添加第三方模块编译安装

```bash
./configure --prefix=/opt/nginx --add-module=/path/to/echo-nginx-module

# 测试
location /hello {
  root   html;
  default_type text/html;
  echo "hello, world!";
  echo $server_port;
  echo  $remote_addr ;
}

curl 127.0.0.1/hello
```

#### 8.3.编译安装脚本

```bash
#!/bin/bash

[[ `id -u` == 0 ]] || echo "请切换到root用户"  || exit 1 

id nginx &>/dev/null
[[ $? == 0 ]] || useradd -s /sbin/nologin -r nginx

[[ -e /apps/nginx ]] || mkdir -p /apps/nginx

yum -y install gcc pcre-devel openssl-devel zlib-devel &>/dev/null

cd /usr/local/src
wget https://nginx.org/download/nginx-1.18.0.tar.gz
[[ $? == 0 ]] || echo "wget下载nginx源码失败,请检查网络" || exit 1
tar zxvf nginx-1.18.0.tar.gz
cd nginx-1.18.0

./configure --prefix=/apps/nginx \
--user=nginx \
--group=nginx \
--with-http_ssl_module \
--with-http_v2_module \
--with-http_realip_module  \
--with-http_stub_status_module \
--with-http_gzip_static_module \
--with-pcre \
--with-stream \
--with-stream_ssl_module \
--with-stream_realip_module \
--add-module=/usr/local/src/echo-nginx-module

if [[ $? != 0 ]];then
    echo "源码编译失败,请检查环境,然后重新执行脚本"
    cd ..
    rm -rf nginx-1.18.0*
    exit 1
fi

make -j 2 && make install
chown -R nginx:nginx /apps/nginx

mkdir -p  /apps/nginx/run
sed -i '/pid/s/.*/pid \/apps\/nginx\/run\/nginx.pid; /' /apps/nginx/conf/nginx.conf
cat > /usr/lib/systemd/system/nginx.service << "EOF"
[Unit]
Description=The nginx HTTP and reverse proxy server
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/apps/nginx/run/nginx.pid
ExecStart=/apps/nginx/sbin/nginx -c /apps/nginx/conf/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID

[Install]
WantedBy=multi-user.target
EOF

ln -sv /apps/nginx/sbin/nginx /usr/sbin/ 
systemctl start nginx && systemctl status nginx

```

#### 8.4配置下载服务器

```bash
# 创建下载服务器的用户名密码
yum install httpd-tools -y
htpasswd  -cbm /apps/nginx/conf/.htpasswd hxh hxh

mkdir -p /apps/nginx/conf/conf.d

vim /apps/nginx/conf/conf.d/download.conf
#---------
server {
         listen   80;
         charset  utf-8;
         server_name   localhost;

         auth_basic           "login password";
         auth_basic_user_file  /apps/nginx/conf/.htpasswd;

         location /download {
            alias   /data/nginx/share;
            if ($request_filename ~* ^.*?\.(txt|doc|pdf|rar|gz|zip|docx|exe|xlsx|ppt|pptx)$){
                add_header Content-Disposition: 'attachment;';
            }
         autoindex     on;
         autoindex_exact_size   off;
         autoindex_localtime    on;
         limit_rate 0;
       }
}
#---------

vim /apps/nginx/conf/nginx.conf
#---------
include /apps/nginx/conf/conf.d/*.conf;
    server {
        listen       80;
        server_name  localhost;
        # ......
#---------
```


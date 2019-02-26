### 配置文件
```
#运行程序用户与用户组
#user  www www;

#
worker_processes  2;

#
worker_rlimit_nofile 65535;

#错误日志路径
error_log  e:/data/logs/nginx_error.log  debug;

#
pid        ./nginx.pid;

#
events {
    worker_connections  65535;
#    use epoll;
}

#
http {
    include       mime.types;
    default_type  application/octet-stream;

    server_names_hash_bucket_size 128;
    client_header_buffer_size 32k;
    large_client_header_buffers 4 32k;
    client_max_body_size 8m;

    sendfile        on;
    tcp_nopush      on;
    tcp_nodelay     on;
    server_tokens   off;

	#静态压缩设置（提升静态文件加载速度）
    gzip            on;
    gzip_comp_level 2;
    gzip_min_length 1024;
    gzip_buffers    4 16k;
    gzip_http_version 1.0;
    gzip_types      text/plain application/x-javascript text/css application/xml;
    gzip_vary       on;

	#php fastcgi设置
    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_read_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;

    keepalive_timeout  60;
	
	proxy_connect_timeout 30;
    proxy_send_timeout    30;
    proxy_read_timeout    30;
	
	#虚拟站点配置路径（导入server配置）
	#include vhost.d/*.conf;
    server {
        #开放端口
        listen 3000;
        
		#站点域名
		server_name 192.168.32.14;
		
		#默认访问文件
		index index.html;
        
		#站点根路径
		root E:\YKB-TStatus;
		
        location /ticket {
            proxy_pass http://192.168.32.80:20018;
			break;
        }
		location ~ .*\.(png|jpeg|jpg|gif|ico)$ {
		  expires 30d;
		}
		location ~ .*\.(js|css)?$ {
		  expires 1d;
		}
	}
}
```

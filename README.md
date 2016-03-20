# nginx-tcp-proxy (Linux)
If there is a requirement to use nginx as a tcp proxy server,do the following steps:

1) cd /opt  
2) wget http://nginx.org/download/nginx-1.5.6.tar.gz  
3) tar -xvzf nginx-1.5.6.tar.gz  
Now we would need a tcp proxy module and its easily available on github  
4) git clone http://github.com/yaoweibin/nginx_tcp_proxy_module  
5) cd nginx-1.5.6  
6) patch -p1 < /opt/nginx_tcp_proxy_module/tcp.patch  
Now we have patched nginx for the required tcp module.  
7) ./configure --add-module=/opt/nginx_tcp_proxy_module/  --conf-path=/etc/nginx/nginx.conf  
(You can compile with other modules along with tcp too.)  
8) make  
9) make install  

Now, we have installed nginx with tcp module.  
Lets move on to configuring nginx.  
  
open /etc/nginx/nginx.conf with your favourite editor and add following lines :  
Now, if I need to proxy all my tcp requests to a service running on port 8118 while my nginx listens on port   8008.  

worker_processes 1;  
events {  
	worker_connections 1024;  
}  

tcp {  
	upstream cluster {  
	server 127.0.0.1:8118;  
	server 127.0.0.1:8118;  
	check interval=3000 rise=2 fall=5 timeout=1000;  
	}  
server {  
	listen 8008;  
	proxy_pass cluster;  
	}  
}  

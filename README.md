nginx解决跨域问题
Q1:nginx配置的server的root文件夹为什么没用
nginx的命令行及其配置文件
1 sudo vim -n /usr/local/etc/nginx/nginx.conf
2 sudo nginx -s reload



配置nginx，重启服务
server {
        listen       8081;  //端口号
        server_name  172.16.1.2;  //本地ip地址
        charset utf-8; //设置编码格式
        index  index.html index.htm;
        root   /Users/liwenqian/Desktop/www;  //nginx的文件存放地址（确保权限，all父文件所有人可读）
        location / {
         #   root   html;
            index  index.html index.htm;
            #proxy_pass  http://prod_web;   //反向代理的ip
        }
       location /partners {    //nginx捕获请求，含有partners的代理到 http://172.16.1.8:8081地址。记得http协议和端口号

            rewrite ^.+partners/?(.*)$ /$1 break;

            proxy_pass http://172.16.1.8:8081;

        }
     location ~ \.php$ {//php的配置
           root           html;
           fastcgi_pass   127.0.0.1:9000;
           fastcgi_index  index.php;
          fastcgi_param  SCRIPT_FILENAME
          $document_root/$fastcgi_script_name;    //重要在设置了自己的跟文件后千万不要忘了设置这个
           include        fastcgi_params;
        }

        因为nginx的默认端口号和apache的默认端口号都是80端口，所以要么修改nginx的端口号要么修改apache的端口号（也要确保都为可读否则会出现403）
 apache的命令行和配置文件
1 cd /ect/apache2    进入存放的地址
2 sudo cp httpd.conf httpd.conf.bak   备份配置文件
3 sudo apachectl -k stop    开／停  apache
4 sudo apachectl -k start
5 sudo lsof -i :80   检验端口号是否被使用，如果没被使用，是空的


http://localhost:8081/partners
最后在index.html中发送的请求为

     $.ajax({
           url:"./partners/store.json",
           type:'GET',
           data:{},
           success:function(data){
             console.log(data);
           },
           error:function(error){

           }
       })

       必须在8081端口下打开index.html才能被nginx反向代理。否在store.json为空值。



       以上就是前端解决跨域问题的nginx配值（Mac）下.
       

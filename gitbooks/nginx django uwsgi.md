# Django 部署(Nginx) #
« Django 部署(Apache)

# 1. 运行开发服务器测试 #

 cd zqxt # 进入项目 zqxt 目录
> python manage.py runserver
 运行开发服务器测试，确保开发服务器下能正常打开网站。

# 2. 安装 nginx 和 需要的包 #
## 2.1 安装 nginx 等软件 ##

 ubuntu / Linux Mint 等，下面简写为 (ubuntu)
安装
> sudo apt-get install nginx
启动、停止和重启
> sudo /etc/init.d/nginx start
> sudo /etc/init.d/nginx stop
> sudo /etc/init.d/nginx restart
或者

> sudo service nginx start
> sudo service nginx stop
> sudo service nginx restart

访问：http://127.0.0.1:8088/


## 2.2 安装 supervisor, 一个专门用来管理进程的工具，我们用它来管理 uwsgi 进程 ##

sudo pip install supervisor

# 3. 使用 uwsgi 来部署 #
 安装 uwsgi
python3 -m pip install uwsgi

测试uwsgi，创建test.py文件：
>def application(env, start_response):
    start_response('200 OK', [('Content-Type','text/html')])
    return [b"Hello World"]
通过uwsgi运行该文件。

>uwsgi --http :8001 --wsgi-file test.py

接下来配置Django与uwsgi连接。此处，假定的我的django项目位置为：

> uwsgi --http :8001 --chdir /home/zf/work/mysite/ --wsgi-file /home/zf/work/mysite/mysite/wsgi.py --master --processes 4 --threads 2 --stats 0.0.0.0:8080

常用选项：

http ： 协议类型和端口号

processes ： 开启的进程数量

workers ： 开启的进程数量，等同于processes（官网的说法是spawn the specified number ofworkers / processes）

chdir ： 指定运行目录（chdir to specified directory before apps loading）

wsgi-file ： 载入wsgi-file（load .wsgi file）

stats ： 在指定的地址上，开启状态服务（enable the stats server on the specified address）

threads ： 运行线程。由于GIL的存在，我觉得这个真心没啥用。（run each worker in prethreaded mode with the specified number of threads）

master ： 允许主进程存在（enable master process）

daemonize ： 使进程在后台运行，并将日志打到指定的日志文件或者udp服务器（daemonize uWSGI）。实际上最常用的，还是把运行记录输出到一个本地文件上。

pidfile ： 指定pid文件的位置，记录主进程的pid号。

vacuum ： 当服务器退出的时候自动清理环境，删除unix socket文件和pid文件（try to remove all of the generated file/sockets）

# Nginx+uwsgi+Django  #



接下来，我们要将三者结合起来。首先罗列一下项目的所需要的文件：

myweb/

├── manage.py

├── myweb/

│   ├── __init__.py

│   ├── settings.py

│   ├── urls.py

│   └── wsgi.py

└── myweb_uwsgi.ini

在我们通过Django创建myweb项目时，在子目录myweb下已经帮我们生成的 wsgi.py文件。所以，我们只需要再创建myweb_uwsgi.ini配置文件即可，当然，uwsgi支持多种类型的配置文件，如xml，ini等。此处，使用ini类型的配置。

> # myweb_uwsgi.ini file
> [uwsgi]
> 
> # Django-related settings
> 
> socket = :8000
> 
> # the base directory (full path)
> chdir   = /home/fnngj/pydj/myweb
> 
> # Django s wsgi file
> module  = myweb.wsgi
> 
> # process-related settings
> # master
> master  = true
> 
> # maximum number of worker processes
> processes   = 4
> 
> # ... with appropriate permissions - may be needed
> # chmod-socket= 664
> # clear environment on exit
> vacuum  = true

socket  指定项目执行的端口号。

　　chdir   指定项目的目录。

　　module  myweb.wsgi ，可以这么来理解，对于myweb_uwsgi.ini文件来说，与它的平级的有一个myweb目录，这个目录下有一个wsgi.py文件。

接下来，切换到myweb项目目录下，通过uwsgi命令读取myweb_uwsgi.ini文件启动项目。
cd /home/fnngj/pydj/myweb/
uwsgi --ini uwsgi.ini

接下来要做的就是修改nginx.conf配置文件。打开/etc/nginx/nginx.conf文件，添加如下内容。 
> sudo vi /etc/nginx/nginx.conf
在http节点里面添加配置
>
        server {
                 listen         80;
                 server_name    psdfeng.ddns.net
                 charset UTF-8;
                 access_log      /var/log/nginx/myweb_access.log;
                 error_log       /var/log/nginx/myweb_error.log;
>
>                 client_max_body_size 75M;
>
                location / {
                include uwsgi_params;
                uwsgi_pass 0.0.0.0:8000;
                uwsgi_read_timeout 2;
                }
                location /static {
                 expires 30d;
                 autoindex on;
                 add_header Cache-Control private;
                 alias /home/zf/work/mysite/static/;
               }
         }

nginx 更改配置文件后需要重启生效。
sudo service nginx restart

判断配置文件是否正确：　　
>nginx -t -c /etc/nginx/nginx.conf

如果是生产环境的话Nginx正在运行，就不要直接stop start 或者 restart  直接reload就行了
 对线上影响最低
 >/etc/init.d/nginx reload 

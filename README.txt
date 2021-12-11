1. when launcing instance and postgresql in security group

2. sudo apt update

3. sudo apt-get -y install postgresql

4. sudo su postgres

	- psql
	- ALTER USER postgres password '#$mypassword1212';
	- \q
	- exit

5. cd /etc/postgresql/10/main/
	- ls
	- sudo nano postgresql.conf
	- (make listen addresses to '*' then save - press = ctrl+s and ctrl+x)

	- sudo nano pg_hba.conf
	- then add the line:
		host       all     all    0.0.0.0/0    md5
		(then save - press = ctrl+s and ctrl+x)

6. sudo service postgresql restart

=======================================================

now go to /home/ubuntu/ this path then :

1. Add Elastic Ip to Instance

2. in security group : allow inbound rules for https, http, and custom port 8000 port anywhere,

3. add public ip into domain AAA variable

4. install python and create virtual env:
	sudo apt-get update
	sudo apt-get install -y python3-venv
	python3 -m venv env

if pillow not installing:
python3 -m pip install --upgrade pip
python3 -m pip install --upgrade Pillow


5. activate virtual env, install req.txt packages
	source env/bin/activate

	pip install django==3.0.2


add database to settings:

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'postgres',
        'USER': 'postgres',
        'PASSWORD': 'password',(just need the password)
        'HOST': 'localhost',
        'PORT': '5432',
    }
}



need to install the package
pip install psycopg2-binary




in settings :
	 
comment - STATICFILES_DIRS

and add:
STATIC_ROOT=os.path.join(BASE_DIR,'static/')
python manage.py collectstatic




6. install nginx:
	sudo apt-get install nginx
   
   change the security group

   install gunicorn:
	pip install gunicorn

7. bind gunicorn with wsgi
	gunicorn --bind 0.0.0.0:8000 project_name.wsgi:application

	allow the host in settings.py
	sudo nano project_name/settings.py

	gunicorn --bind 0.0.0.0:8000 project_name.wsgi:application

   try to run :
	ip:8000


sudo systemctl reload nginx


To make it permanent running state
	sudo apt-get install supervisor


cd /etc/supervisor/conf.d/
	sudo touch gunicorn.conf

sudo nano gunicorn.conf:

[program:gunicorn]
directory=/home/ubuntu/project
command=/home/ubuntu/env/bin/gunicorn --workers 3 --bind unix:/home/ubuntu/project/app.sock project_name.wsgi:application
autostart = true
autorestart=true
stderr_logfile= /var/log/gunicorn/gunicorn.err.log
stdout_logfile= /var/log/gunicorn/gunicorn.out.log

[group:guni]
programs:gunicorn






sudo mkdir /var/log/gunicorn

sudo supervisorctl reread

sudo supervisorctl update

sudo supervisorctl status

sudo systemctl reload nginx



cd /etc/nginx/sites-available/

sudo nano default

## make some line into comments - example : root /var/www/html;, index index.html, try_files ...

and add in location:
include proxy_params;
proxy_pass http://unix:/home/ubuntu/project/app.sock;


sudo systemctl reload nginx


show statics:
===============
sudo nano default

location /static/{
	autoindex on;
	alias /home/ubuntu/final_test/static/;
	}


sudo systemctl reload nginx

python manage.py collectstatic


to make HTTPS request:
===========================
1. sudo apt-add-repository -r ppa:certbot/certbot
2. sudo apt-get install python3-certbot-nginx
3. sudo certbot --nginx -d YourDomain.com -d www.yourDomain.com 

	- 2

sudo systemctl reload nginx




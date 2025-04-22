# django-vps-deployment-nginx-gunicorn

> create a directory

>create virtual environment

> install/copy django project

>install gunicorn inside VENV

**GUnicorn**
```bash
pip install gunicorn # install gunicorn
gunicorn --bind 0.0.0.0:8001 core.wsgi:application # run django via gunicorn
```

***Supervisor***
```bash
sudo apt install supervisor

# create config
sudo nano /etc/supervisor/conf.d/django-demo.conf
``` 


***Supervisor Config***
```c
[program:myproject]
directory=/home/youruser/myproject
command=/home/youruser/myproject/venv/bin/gunicorn myproject.wsgi:application --bind 127.0.0.1:8001
autostart=true
autorestart=true
stderr_logfile=/var/log/myproject.err.log
stdout_logfile=/var/log/myproject.out.log
user=youruser
```


**Supervisor Related Command**
```bash
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl start django-demo
sudo supervisor restart all
```



**NGINX INSTALLATION**
```bash
sudo apt update
sudo apt install nginx
```

check that it's running

```bash
sudo systemctl status nginx
```



***NGINX CONFIGURATION***
```bash
sudo nano /etc/nginx/sites-available/project
```

sample settings

```nginx
server {
    listen 80;
    server_name yourdomain.com;  # Or your IP address

    location = /favicon.ico { access_log off; log_not_found off; }
    location /static/ {
        alias /home/youruser/myproject/staticfiles/;  # Adjust path
    }

    location / {
        proxy_pass http://127.0.0.1:8001;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

```

> replace your domain and your actual project path


**ENABLE CONFIG**
```bash
sudo ln -s /etc/nginx/sites-available/myproject /etc/nginx/sites-enabled/
sudo nginx -t   # test config
sudo systemctl restart nginx
```



**Django Settings**

settings.py
```python
STATIC_ROOT = os.path.join(BASE_DIR, "staticfiles/")
```

```bash
python manage.py collectstatic
```



***CERTBOT FOR SSL (HTTPS)***

```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx
```


**GET YOUR SSL CERTIFICATE**

```bash
sudo certbot --nginx
```


**AUTO RENEW CERTIFICATE (recommended)**

```bash
sudo certbot renew --dry-run
```


**Bonus Permissions Check**
```bash
sudo chown -R www-data:www-data /home/benj/django-demo/staticfiles
sudo chmod -R 755 /home/benj/django-demo/staticfiles
```


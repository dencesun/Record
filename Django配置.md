# Automatic deploy project


# 1.系统更新
```
sudo apt-get update
sudo apt-get upgrade
```
# 2.安装必要软件
```
sudo apt-get install nginx
sudo apt-get install git python3 python3-pip
sudo pip3 install virtualenv
```
# 3.启动nginx服务
```
sudo service nginx start
```
# 4.项目配置
##### 将django的settings.py做如下修改
```
# others settings....
STATIC_URL = '/static/'
# add additional settings
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
# close debug mode
DEBUG = False
# add domain name
ALLOWED_HOSTS = ['127.0.0.1', 'localhost ', 'your domain name(.test.com)']
```
# 5.生成requirements.txt
```
# enter the project's root directory(our project is ~/ChainQuantization/..)
pip freeze > requirements.txt
```
# 6.拉取工程项目到服务器
```
# 在服务器创建保存网站的文件夹
mkdir -p ~/sites/blockchain
# virtualenv创建虚拟环境
cd ~/sites/blockchain
virtualenv --python=python3 env
# 使用ls命令检查env是否创建成功
# 拉取工程项目到服务器
cd ~/sites/blockchain
git clone your github's location
```
# 7.安装项目依赖
```
# active env
cd ~/sites/blockchain
source env/bin/activate
# enter your project's directory
cd ChainQuantization
pip install -r requirements.txt
```
# 8.收集静态文件
```
cd ~/sites/blockchain/ChainQuantization
python manage.py migrate
```
# 9.创建超级管理用户
```
python manage.py createsupteruser
```
# 10.配置nginx
```
cd /etc/nginx/sites-available/
sudo vim blockchain
# demo, change later
server {
    charset utf-8;
    listen 80;
    server_name demo.zmrenwu.com; ①

    location /static { ②
        alias /home/yangxg/sites/demo.zmrenwu.com/django-blog-tutorial/static; 
    }

    location / { ③
        proxy_set_header Host $host;
        proxy_pass http://unix:/tmp/demo.zmrenwu.com.socket;
    }
}
# save this file
# create symbol link
sudo ln -s /etc/nginx/sites-available/blockchain /etc/nginx/sites-enabled/blockchain
```
# 11.使用gunicorn
```
cd ~/sites/blockchain/ChainQuantization
pip install gunicorn
// edit later
gunicorn --bind ***
```
# 12.自动启动gunicorn
```
sudo mkdir -p /usr/lib/systemd/system
sudo vim /usr/lib/systemd/system/blockchain.service

[Unit]
After=syslog.target network.target remote-fs.target nss-lookup.target
[Service]
# your user
User=ubuntu
# 你的目录
WorkingDirectory=~/sites/blockchain/ChainQuantization
# gunicorn start change later
ExecStart=~/sites/blockchain/env/bin/gunicorn --bind unix:/tmp/siar.socket blogproject.wsgi:application
Restart=on-failure
[Install]
WantedBy=multi-user.target

# save

sudo symtemctl start blockchain.service
# check start successful
ps -ef | grep gunicorn
```
# 13. 如果代码更新了
```
git clone ****
sudo service nginx reload
sudo symtemctl start blockchain.service
```

# ssl配置
```
sudo apt-get update
sduo apt-get install software-properties-common
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python-certbot-nginx

sudo cetbot --nginx
# when installing, you need to enter your web domain name
```

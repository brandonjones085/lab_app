# lab_app
raspberry pi temp/humidity 
This project was made following the Tech Exploration Raspberry pi full stack tutorial.



Installed Raspbian Lite

create ssh file in boot

create wpa_supplicant.conf 
network={
    ssid=NailPolish
    psk="password"
    key_mgmt=WPA-PSK
}

#ssh 
ssh pi@IP_ADDRESS
#default password
raspberry


#run to configure
sudo raspi-config

#enable root for ssh
sudo su
nano /etc/ssh/sshd_config

#uncomment
PermitRootLogin  yes
/etc/init.d/ssh restart
passwd root
#Rasp
run python3 AdafruitDHT.py 2302 2


#install python3 pip
sudo apt-get install python3-pip

#install gpis
pip3 install rpi.gpio

#clone adafruit DHT and install GIT
sudo apt-get install git-core
git config --global user.email 
git config --global user.name 
git clone https://github.com/adafruit/Adafruit_Python_DHT.git
ls -al
cd Adafruit_Python_DHT
sudo python3 setup.py install

#move into examples
cd examples
python3 AdafruitDHT.py 2302 17

##essential packages
sudo apt-get install build-essential 
sudo apt-get install libncurses5-dev libncursesw5-dev libreadline6-dev libffi-dev
sudo apt-get install libbz2-dev libexpat1-dev liblzma-dev zlib1g-dev libsqlite3-dev libgdbm-dev tk8.5-dev libssl-dev openssl
sudo apt-get install python-dev

##python
mkdir python-source
cd python-source/
wget https://www.python.org/ftp/python/3.6.4/Python-3.6.4.tgz
tar zxvf Python-3.6.4.tgz
cd Python-3.6.4/
./configure --prefix=/usr/local/opt/python-3.6.4
make
sudo make install
/usr/local/opt/python-3.6.4/bin/python3.6 --version

##web application and virtual env
sudo su 
cd /var
mkdir www
cd www
mkdir lab_app
cd lab_app
/usr/local/opt/python-3.6.4/bin/python3.6 -m venv . 
ls -al 
. bin/activate

##nginx
. bin/activate
apt-get install nginx
apt-get update
##check ip of pi

#flask
pip install flask

##install uwsgi
pip install uwsgi


##new config file for nginx
rm /etc/nginx/sites-enabled/default
nano lab_app_nginx.conf
server{
	listen 80; 
	server_name localhost; 
	charset utf-8;
	client_max_body_size 75M; 

	location /static {
		root /var/www/lab_app;
	}

	location / { try_files $uri @labapp; }
	location @labapp{
		include uwsgi_params; 
		uwsgi_pass unix:/var/www/lab_app/lab_app_uwsgi.sock;
	}
}

##link files
ln -s /var/www/lab_app/lab_app_nginx.conf /etc/nginx/conf.d/
ls -al /etc/nginx/conf.d/

##uwsgi conf file
nano lab_app_uwsgi.ini

#Full path: /var/www/lab_app/lab_app_uwsgi.ini

[uwsgi]
#application's base folder
base = /var/www/lab_app

#python module to import
app = hello
module = %(app)

home = %(base)
pythonpath = %(base)

#socket file's location
socket = /var/www/lab_app/lab.sock

#permissions for the socket file
chmod-socket    = 666

#the variable that holds a flask application inside the module imported at line #6
callable = app

#location of log files
logto = /var/log/uwsgi/lab_app_uwsgi.log


###make log directory
mkdir /var/log/uwsgi

##start uwsgi
bin/uwsgi --ini /var/www/lab_app/lab_app_uwsgi.ini

###auto start uwsgi with system d
nano /etc/systemd/system/emperor.uwsgi.service
[Unit]
Description=uWSGI Emperor
After=syslog.target

[Service]
ExecStart=/var/www/lab_app/bin/uwsgi --ini /var/www/lab_app/lab_app_uwsgi.ini

RuntimeDirectory=uwsgi
Restart=always
KillSignal=SIGQUIT
Type=notify
StandardError=syslog
NotifyAccess=all

[Install]
WantedBy=multi-user.target


##start systemd
systemctl start emperor.uwsgi.service
systemctl status emperor.uwsgi.service
systemctl enable emperor.uwsgi.service
reboot


##install sqlite
cd /var/www/lab_app
sudo su
. bin/activate
apt-get install sqlite



##create sql files
sqlite3 sample.sql
.help
begin;
create table temperatures (rDatetime datetime, sensorID text, temp numeric); 
insert into temperatures values (datetime(' now'), " 1", 25); 
commit; 


#static directory
mkdir static
cd static
mkdir css
mkdir images
nano a_static_file.html
mkdir templates

##debugging with log files
app.debug = True
/var/log/uwsgi/%n.log
tail command ---- tail -n 100 /var/log/uwsgi/lab_app_uwsgi


##install DHT library and rpi.gpio in virtual env
pip install rpi.gpio
git clone https://github.com/adafruit/Adafruit_Python_DHT.git
cd Adafruit_Python_DHT
python setup.py install
python3 AdafruitDHT.py 2302 2



##restart uwsgi
systemctl restart emperor.uwsgi.service


###create sqlite3 temp and humidity database
sqlite3 lab_app.db
begin; 
create table temperatures (rDatetime dateimte, sensorID text, temp numeric); 
insert into temperatures values (datetime(CURRENT_TIMESTAMP), "1",25); 
insert into temperatures values (datetime(CURRENT_TIMESTAMP), "1",25.10); 
commit; 

##check
.tables

##create humidity table
begin; 
create table humidities (rDatetime datetime, sensorID text, hum numeric); 
insert into humidities values (datetime(CURRENT_TIMESTAMP), "1", 51); 
insert into humidities values (datetime(CURRENT_TIMESTAMP), "1", 51.10); 
commit; 


##create python script to read sensors and place within database
env.db

###invoke cron text editor
crontab.gutu
crontab -e
2


##plotly
pip install plotly
pip install plotly --upgrade


##configure plotly
import plotly
api key = 


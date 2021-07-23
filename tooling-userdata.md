#!/bin/bash
mkdir /var/www
mount -t efs -o tls,accesspoint=fsap-0b5621b239d1f2a76 fs-ba90ee0e:/ /var/www/
yum install -y httpd 
systemctl start httpd
systemctl enable httpd
yum module reset php -y
yum module enable php:remi-7.4 -y
yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
systemctl start php-fpm
systemctl enable php-fpm
git clone https://github.com/Livingstone95/devops-tooling.git
cp -R /devops-tooling/html/*  /var/www/html/
cd /var/www/html/
touch healthstatus
sed -i "s/localhost/acs-database.cdqpbjkethv0.us-east-1.rds.amazonaws.com/g" db_conn.php 
sed -i "s/root/tooling/g" db_conn.php
sed -i "s/pass/t00l\!n\#/g" db_conn.php
sed -i "s/dare/toolingdb/g" db_conn.php
chcon -t httpd_sys_rw_content_t /var/www/html/ -R
systemctl restart httpd



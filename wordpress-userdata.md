#!/bin/bash
mkdir /var/www/
mount -t efs -o tls,accesspoint=fsap-0dbc833e213cfff75 fs-ba90ee0e:/ /var/www/
yum install -y httpd 
systemctl start httpd
systemctl enable httpd
wget http://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz
rm -rf latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php
cp -R wordpress/* /var/www/html/
cd /var/www/html/
touch healthstatus
sed -i "s/localhost/acs-database.cdqpbjkethv0.us-east-1.rds.amazonaws.com/g" wp-config.php 
sed -i "s/username_here/wordpress/g" wp-config.php 
sed -i "s/password_here/w0rdpr33/g" wp-config.php 
sed -i "s/database_name_here/wordpressdb/g" wp-config.php 
chcon -t httpd_sys_rw_content_t /var/www/html/ -R
systemctl restart httpd







# softwarelab3Q1
There are four software components that make up Linux-based web servers. The software stack is made up of several elements, which are grouped in layers and support one another. This underlying stack is the foundation for websites and web applications. Typical software elements of a conventional LAMP stack include:
Linux: Our first layer is the operating system (OS). The stack model's foundation is laid by Linux. This layer is followed by all other layers.
Web server software, often Apache Web Server, makes up the second layer. The Linux layer is followed by this layer. The translation from web browsers to the proper website is done by web servers.
Databases reside in our third layer.MySQL holds information that programming can query to build websites. Apache/layer 2 and MySQL typically sit on top of the Linux layer. High end configurations allow for the offloading of MySQL to a different host server.
PHP: Our fourth and final layer is positioned on top of them all. PHP and/or other web programming languages make up the scripting layer. Within this layer, websites and web applications function.

->

One of the most widely used web servers worldwide is Apache.
It is a fantastic default option for hosting a website because it is well-documented and has been widely used for a significant portion of the history of the web.
So, execute the next command:

 Install Apache:

sudo apt update 
sudo apt Install Apache2 

type Y and hit ENTER. The installation will then start.

->

Ensure that HTTP and HTTPS traffic is permitted by your firewall.
As a result, you can do the following to see if UFW has an application profile for Apache:

sudo ufw app list 
Output
Available applications:
  Apache
  Apache Full
  Apache Secure
  OpenSSH


sudo ufw app info "Apache Full"
Profile: Apache Full
Title: Web Server (HTTP,HTTPS)
Description: Apache v2 is the next generation of the omnipresent Apache web
server.
 
Ports:
  80,443/tcp


For this profile, accept incoming HTTP and HTTPS traffic:

sudo uff allow in “Apache Full”

By going to your server's public IP address in your web browser, you can perform an immediate spot check to make sure everything went according to plan.

http://your_server_ip

You'll see the standard Ubuntu Apache web page, which is provided for testing and informational reasons.

->

Now that your web server is operational, you should install MySQL.
A database management system is MySQL. Basically, it will set up access to databases that your website can use to store data and organise it.
sudo apt install mysql-server 
sudo mysql_secure_installation

Note: Failed! Error: SET PASSWORD has no significance for user 'root'@'localhost' as the authentication method used doesn't store authentication data in the MySQL server. Please consider using ALTER USER instead if you want to change authentication parameters.

If you get this error > follow these steps

sudo mysql

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'mynewpassword';

And now, you are able to run mysql_secure_installation command.

sudo mysql_secure_installation

->

Your setup's PHP component will parse code to display dynamic information.
It can execute scripts, establish a connection to your MySQL databases to get data, and then deliver the information processed to your web server for display.
Use the apt system to install PHP once more. Include some assistance packages this time as well so that PHP code can communicate with your MySQL database and operate under the Apache server:

sudo apt install php libapache2-mod-php php-mysql

Most of the time, you'll need to change how Apache provides files when a directory is requested. Currently, Apache will check for a file named index.html when a user requests a directory from the server. Make Apache search for an index.php file first because we want to instruct the web server to prioritise PHP files above others.
To achieve this, enter the following command to launch a text editor with root permissions and open the dir.conf file:

sudo nano /etc/apache2/mods-enabled/dir.conf

<IfModule mod_dir.c>
    DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
</IfModule>

Move index.php at the first place after directory index
 
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>

Restart apache web server

sudo systemctl restart apache2   
sudo systemctl status apache2

->

With the Apache web server, you can host several domains on a single server by using virtual hosts to encapsulate configuration information. Your domain will be the domain name we create; however, you should substitute your own domain name here.

One server block is enabled by default in Apache on Ubuntu, and it is set up to serve files from the /var/www/html directory. While this is effective for a single site, hosting many sites might make it cumbersome. Instead of making changes to /var/www/html, let's build a directory structure within /var/www for our your domain site and leave /var/www/html in place to act as the default directory if a client request doesn't match any other sites.

sudo mkdir /var/www/your_domain
sudo chown -R $USER:$USER /var/www/your_domain
sudo chmod -R 755 /var/www/your_domain
sudo nano /var/www/your_domain/index.html


 
<html>
    <head>
        <title>Welcome to Your_domain!</title>
    </head>
    <body>
        <h1>Success!  The your_domain server block is working!</h1>
    </body>
</html>

Furthermore, a virtual host file must be created with the appropriate directives in order for Apache to deliver this content. Let's create a new configuration file at /etc/apache2/sites-available/your_domain.conf rather than directly alter the default one at /etc/apache2/sites-available/000-default.conf:

sudo nano /etc/apache2/sites-available/your_domain.conf

<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    ServerName your_domain
    ServerAlias www.your_domain
    DocumentRoot /var/www/your_domain
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

You'll see that we've changed ServerAdmin to an email that the your domain site administrator can access and DocumentRoot to point to our new directory. In addition, we've added two directives: ServerName, which provides the base domain that must match for this virtual host definition, and ServerAlias, which specifies additional names that must match as if they were the base name:

sudo a2ensite your_domain.conf
sudo a2dissite 000-default.conf
sudo apache2ctl configtest  
sudo systemctl restart apache2

Your domain name ought should now be served by Apache. You may verify this by going to http://your domain.

->

Create a very simple PHP script called info.php to verify that your system is set up correctly for PHP. This file must be saved to your web root directory for Apache to find it and serve it correctly:
sudo nano /var/www/your_domain/info.php
 

<?php
phpinfo();
?>
 
You can now check to see if your web server can display the content produced by this PHP script appropriately. Visit this page in your web browser to test it out.
Consequently, you will once more want your server's public IP address.

http://your_domain/info.php

->

Testing database connection from php.

Create a database using the CREATE statement:
	•	CREATE DATABASE example_database;

Upon installation, MySQL creates a root user account which you can use to manage your database. This user has full privileges over the MySQL server, meaning it has complete control over every database, table, user, and so on. Because of this, it’s best to avoid using this account outside of administrative functions. This step outlines how to use the root MySQL user to create a new user account and grant it privileges.

	•	CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
The general syntax for granting user privileges is as follows:

	•	GRANT ALL ON example_database.* TO 'example_user'@'%’; 

Next, verify that the database was created by showing a list of all databases. Use the SHOW statement:
	•	SHOW DATABASES;


Note: How to see all MySQL users ?
	1	> mysql -u root -p  
	2	Enter password: *********  
	3	mysql> use mysql;  
	4	Database changed  
	5	mysql> SELECT user FROM user;  


Create a table using the CREATE command. Using the information from our example, the command is:

	•	CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),	PRIMARY KEY(item_id));

Insert  information in column order.Use the INSERT command:

	•	INSERT INTO example_database.todo_list (content) VALUES ("My first important item");

Use the SELECT command to display the table:

	•	SELECT * FROM example_database.todo_list;
You can now write the PHP script necessary to connect to MySQL.
Visit
	•	http://your_domain_or_IP/todo_list.php 




















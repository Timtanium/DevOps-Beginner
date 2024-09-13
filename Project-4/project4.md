# Setup WordPress Website Using LAMP Stack

Setting up a WordPress website using the LAMP stack (Linux, Apache, MySQL, PHP) is a powerful way to create a robust and scalable web presence. This project will guide you through each step of the process, from setting up your server environment to installing WordPress and configuring it for optimal performance. Whether you're a seasoned developer or a beginner, this tutorial will provide you with the knowledge and skills needed to deploy a fully functional WordPress site. By the end of this guide, you'll have a solid understanding of how to leverage the LAMP stack to build and manage your own WordPress website, ensuring a secure, efficient, and customizable platform for your content.

**Key Features:**

- Step-by-step instructions for installing and configuring LAMP components.
- Secure database creation for your WordPress website.
- User-friendly guide for completing the WordPress installation process.

**Benefits:**

- Gain independence by managing your own WordPress website.
- Understand the core technologies behind WordPress.
- Customize your website to your exact needs.
- Embrace the flexibility and power of WordPress.

## Introduction

Before we begin, let's review the tools we'll be using throughout this project.

### Linux

Linux is the cornerstone of the LAMP stack, serving as the operating system that underpins the entire infrastructure. As an open-source and highly customizable platform, Linux provides a stable and secure environment for hosting web applications. Its efficiency in handling processes and resources makes it an ideal choice for web servers. In a LAMP stack, Linux coordinates the interaction between Apache (the web server), MySQL (the database), and PHP (the scripting language), ensuring seamless operation and communication between these components. This foundation allows for high performance, flexibility, and scalability, making Linux a crucial element in the successful deployment and management of a LAMP-based WordPress website.

### Apache

Apache is a critical component of the LAMP stack, acting as the web server that handles client requests and serves web content. As an open-source and widely used web server software, Apache is known for its reliability, security, and flexibility. In a LAMP stack, Apache processes incoming HTTP requests, fetching and delivering web pages to users' browsers. It also supports various modules that extend its functionality, such as URL redirection, authentication, and load balancing. By efficiently managing web traffic and providing a stable platform for running web applications, Apache ensures that your WordPress website runs smoothly and can handle a large number of concurrent users.

### MySQL

MySQL is the database management system within the LAMP stack, responsible for storing and managing the data that powers your web applications. As an open-source relational database, MySQL offers robust performance, scalability, and security. In the context of a LAMP stack, MySQL works closely with PHP to handle data operations such as storing user information, retrieving content, and managing transactions. For a WordPress website, MySQL stores all the content, user data, settings, and other essential information, allowing for dynamic content generation and efficient data retrieval. By ensuring data integrity and providing powerful querying capabilities, MySQL plays a crucial role in maintaining the functionality and performance of a WordPress site on the LAMP stack.

### PHP

PHP is the scripting language in the LAMP stack, responsible for generating dynamic web content. As a server-side language, PHP processes code on the web server to create HTML content that is then sent to the user's browser. In the LAMP stack, PHP works in tandem with Apache to handle web requests and interact with the MySQL database to retrieve and manipulate data. For a WordPress website, PHP executes the core logic that powers the site, managing everything from user authentication to content management and plugin functionality. By seamlessly integrating with Apache and MySQL, PHP ensures that your WordPress site is dynamic, interactive, and capable of delivering a rich user experience.

---

## Project 4

|S/N | Project Tasks                                                       |
|----|---------------------------------------------------------------------|
| 1  |Deploy an Ubuntu Server                                              |
| 2  |Set up your LAMP stack on the server                                 |
| 3  |Configure the wordpress Application                                  |
| 4  |Map the IP address to the DNS A record                               |
| 5  |Validate the WordPress website setup by accessing the web address.   |

## Key Concepts Covered

- AWS (EC2 and Route 53)
- Linux(Ubuntu)
- Apache
- MySQL
- PHP
- Wordpress
- DNS
- SSL (certbot)
- OpenSSL command

## Checklist

- [x] Task 1: Deploy an Ubuntu Server
- [x] Task 2: Set up your LAMP stack on the server
- [x] Task 3: Configure the wordpress Application
- [x] Task 4: Map the IP address to the DNS A record
- [x] Task 5: Validate the WordPress website setup by accessing the web address.

## Documentation

I refrenced Project1[****](https://github.com/StrangeJay/devops-beginner-bootcamp/blob/main/project1/project1.md) for guidance on spinning up an Ubuntu server.

- I Set an inbound rule for MYSQL in my security group. I clicked on Security① and selected the Security group②.********

![60](img/Screenshot%20(60).png)

- I clicked on **Edit inbound rules**.

![61](img/Screenshot%20(61).png)

- I clicked on **Add rule**.

![62](img/Screenshot%20(62).png)

- I clicked on **Custom TCP**① and selected **MySQL/Aurora**②.

![63](img/Screenshot%20(63).png)

- I entered my **IP address①** I want to allow access and I clicked **Save rules②**.

![63](img/Screenshot%20(63).png)

> [!NOTE]
You can choose 0.0.0.0/0 to permit access from anywhere, but for added security, Ied restrict access to MySQL exclusively to my Web Server’s IP address. In the Inbound Rule configuration, I specified the source as /32

- I opened my **terminal** and connected to my Ubuntu server via SSH.

### Install Apache

- To install Apache, I ran the following commands in my terminal.

**`sudo apt update`**

**`sudo apt install apache2`**

- To enable Apache to start on boot, I executed **`sudo systemctl enable apache2`①**, and then verified its status with the **`sudo systemctl status apache2`②** command.

- Now, let's check if our server is running and accessible both locally and from the Internet by executing the following command: **`curl http://localhost:80`**.

![5](img/5.png)

*Now, it's time to test how our Apache HTTP server responds to requests from the Internet.*

- I copied my **public IPv4 address** from my EC2 dashboard.

![6](img/6.png)

- I opened a web browser of my choice (chrome) and tried accessing the following URL: **`http://<Public-IP-Address>:80`**

![7](img/7.png)

- Because the installation was successful, I saw this page.

![8](img/8.png)

---

### Install MYSQL

- To install this software using 'apt', I ran the command **`sudo apt install mysql-server`**. When prompted, I confirmed the installation by typing 'Y' and then pressing ENTER.

![9](img/9.png)

- After the installation is complete, I logged in to the MySQL console by typing: **`sudo mysql`**.

> [!NOTE]
It's recommended that you run a security script included with MySQL to enhance security. Before running the script, set a password for the root user using the default authentication method **mysql_native_password**. For this project, I'll define the password as "**pass**", but you can choose any password you prefer.

- I ran the following command to set the password for the root user with the MySQL native password authentication method: **`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'pass';`**. I exited the MySQL shell when done by typing **`exit`**.

![10](img/10.png)

- I started the interactive script by running: **`sudo mysql_secure_installation`①**. Answered **y**② for yes, or any other key to continue without enabling specific options.

![11](img/11.png)

- I set my **password validation policy level**.

![12](img/12.png)

> [!NOTE]
I set my password validation policy level to 0 because I don't require much security, as I will be terminating all resources immediately after this project. However, on the job, it's advised to use the strongest level, which is 2.

- I enabled MySQL to start on boot by executing **`sudo systemctl enable mysql`①**, and then confirmed its status with the **`sudo systemctl status mysql`②** command.

![13](img/13.png)

---

### Install PHP

- I installed PHP along with required extensions by running the following script: **`sudo apt install php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip`**.

![14](img/14.png)

**`sudo apt install php libapache2-mod-php php-mysql`**

![15](img/15.png)

- I confirmed the downloaded PHP version by running **`php -v`**.

![16](img/16.png)

### Creating A Virtual Host For Your Website Using Apache

- I created the directory for Projectlamp using the 'mkdir' command as follows:
**`sudo mkdir /var/www/projectlamp`①** and assigned ownership of the directory to our current system user using:
**`sudo chown -R $USER:$USER /var/www/projectlamp`②**.

![17](img/17.png)

- I created and opened a new configuration file in Apache's sites-available directory using my preferred command-line editor:
**`sudo vi /etc/apache2/sites-available/projectlamp.conf`**.

- Creating this produced a new blank file. I pasted the configuration text provided below into it:

```
<VirtualHost *:80>

ServerName projectlamp

ServerAlias www.projectlamp

ServerAdmin webmaster@localhost

DocumentRoot /var/www/projectlamp

ErrorLog ${APACHE_LOG_DIR}/error.log

CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

![18](img/18.png)

- I saved my changes by pressing the **`Esc`** key, then typed **`:wq`** and pressed **`Enter`**.

![19](img/19.png)

- I ran the ls command **`sudo ls /etc/apache2/sites-available`①** to show the **new file②** in the sites-available directory.

![20](img/20.png)

- We can now enable the new virtual host using the a2ensite command: **`sudo a2ensite projectlamp`**.

![21](img/21.png)

- To disable Apache's default website, I used the a2dissite command. I typed: **`sudo a2dissite 000-default`**.

![22](img/22.png)

- To ensure my configuration file doesn’t contain syntax errors, I ran: **`sudo apache2ctl configtest`**. I saw **"Syntax OK"** in the output because configuration is correct.

![23](img/23.png)

- Finally I ran: **`sudo systemctl reload apache2`**. This will reload Apache for the changes to take effect.

> [!NOTE]
Our new website is now active, but the web root **`/var/www/projectlamp`** is still empty. Let's create an **`index.html`** file in that location to test that the virtual host works as expected.

- To create the **index.html** file with the content **"Hello LAMP from Timmy"** in the /var/www/projectlamp directory, I used the following command: **`sudo echo 'Hello LAMP from Timmy' > /var/www/projectlamp/index.html`**.

![24](img/24.png)

- Now, I opened my web browser and tried to access my website using the IP address:

**`http://<EC2-Public-IP-Address>:80`**

> [!NOTE]
I replaced **`<EC2-Public-IP-Address>`** with my actual EC2 instance's public IP address(3.88.173.72).

![25](img/25.png)

- I removed the index.html file by running the following command: **`sudo rm /var/www/projectlamp/index.html `**

### Enable PHP On The Website

With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. To change the precedence of index files (such as index.php over index.html) in Apache, I'll need to edit the dir.conf file. Here’s how I did it:

- I edited the dir.conf file using a text editor (such as nano or vi): **`sudo nano /etc/apache2/mods-enabled/dir.conf`**

- I looked for the DirectoryIndex directive within this file. It typically looks like this:

```
<IfModule mod_dir.c>
    DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
</IfModule>
```

- To prioritize **index.php** over **index.html**, I moved **index.php** to the beginning of the list, like this:

```
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```

![26](img/26.png)

- I pressed **`ctrl` + `x`①** on my keyboard to save and exit.

![27](img/27.png)

- I typed **`y`②** to save the changes.

![28](img/28.png)

- When prompted to confirm the file name, I simply pressed **`ENTER`③** to save the changes with the existing file name.

![29](img/29.png)

- Finally, I reloaded Apache for the changes to take effect: **`sudo systemctl reload apache2`**.

![30](img/30.png)

Now, Apache will prioritize index.php over index.html when both files exist in the same directory.

- To create a new file named index.php inside my custom web root folder (/var/www/projectlamp), I used the following command to open it in the nano text editor: **`nano /var/www/projectlamp/index.php`**.

- This created a new file. I copied and pasted the following PHP code into the new file:

```
<?php

phpinfo();
```

![31](img/31.png)

- Once I've saved and closed the file, I went back to my web browser and refreshed the page. I saw something like this:

![32](img/32.png)

> [!NOTE]
This page provides information about my server from the perspective of PHP. It is useful for debugging and to ensure that my settings are being applied correctly.

Because I can see this page in my browser, that means my PHP installation is working as expected.
After verifying the relevant information about my PHP server through that page, I had to remove the file I created, as it contains sensitive information about my PHP environment and my Ubuntu server. I used the rm command to do so:
**`sudo rm /var/www/projectlamp/index.php`**.

I can always recreate this page if I need to access the information again later.

---

### Install Wordpress

After setting up my LAMP environment, I can start installing WordPress. First, I'll download the WordPress installation files and place them in the default web server root directory: **/var/www/html**.

- I navigated to the directory using the cd command **`cd /var/www/html`**, and then downloaded the WordPress installation files using the following command: **`sudo wget -c http://wordpress.org/latest.tar.gz`**

![33](img/33.png)

- I extracted the files from the downloaded WordPress archive using the command: **`sudo tar -xzvf latest.tar.gz`**

![34](img/34.png)

- I ran the command **`ls -l`** to confirm the existence of the **wordpress** directory in the current location (`/var/www/html`).

![35](img/35.png)

> [!NOTE]
The files must be owned by the user of your web server. Identify the web server's user and assign the appropriate permissions accordingly. The user **`www-data`** is widely adopted as the default user for web server processes, especially on Ubuntu and Debian systems. This user oversees the operation of web server software (like Apache or Nginx) and manages incoming web requests. However, for verification and for tasks involving services that may not have a predefined user, checking the user of the web server is advisable.

- I checked the user running my web server with the command: **`ps aux | grep apache | grep -v grep`**.

![36](img/36.png)

*This command filters processes related to Apache (apache2 on Ubuntu) and displays information about the user running those processes.*

- I granted ownership of the WordPress directory and its files to the web server user **(www-data)** by running the command: **`sudo chown -R www-data:www-data /var/www/html/wordpress`**.

![37](img/37.png)

### Create a Database For Wordpress

- I accessed my MySQL root account with the following command: **`sudo mysql -u root -p`①**. Entered the **password②** I set earlier when prompted.

![38](img/38.png)

- To create a separate database named wp_db for WordPress to manage, execute the following command in the MySQL prompt: **`CREATE DATABASE wp_db;`**

![39](img/39.png)

> [!NOTE]
This command allows you to create a new database (**wp_db**) within your MySQL environment. Feel free to name it as you prefer.

- To access the new database, you can create a MySQL user account with a strong password using the following command:
**`CREATE USER Timmy@localhost IDENTIFIED BY 'wp-password';`**

![40](img/40.png)

*Replace 'wp-password' with your preferred strong password for the MySQL user account.*

- To grant my created user (Tim@localhost) all privileges needed to work with the wp_db database in MySQL, I used the following commands:

```
GRANT ALL PRIVILEGES ON wp_db.* TO Timmy@localhost;
FLUSH PRIVILEGES;
```

![41](img/41.png)

> [!NOTE]
This grants all privileges **(ALL PRIVILEGES)** on all tables within the wp_db database **(`wp_db.*`)** to the user Timmy when accessing from localhost. The FLUSH PRIVILEGES command ensures that MySQL implements the changes immediately.

- I typed **`exit`** to exit the MySQL shell.

![42](img/42.png)

- I granted executable permissions recursively (-R) to the wordpress folder using the following command: **`sudo chmod -R 777 wordpress/`**

![43](img/43.png)

> [!NOTE]
This command sets read (r), write (w), and execute (x) permissions for the owner, group, and others on all files and directories within the wordpress folder. Using 777 permissions is quite permissive and may not be necessary for all files and folders; consider adjusting permissions based on security requirements.

- I changed into the WordPress directory by running the command: **`cd wordpress`**.

![44](img/44.png)

### Configure Wordpress

Once I established a database for WordPress, the next crucial step is setting up and configuring WordPress itself. To begin, I need to create a configuration file tailored for WordPress.

- I renamed the sample WordPress configuration file with the command: **`mv wp-config-sample.php wp-config.php`**.

- I edited the **`wp-config.php`** file using the command: **`sudo nano wp-config.php`**.

![45](img/45.png)

- I updated the database settings in the **`wp-config.php`** file by replacing placeholders like **database_name_here**, **username_here**, and **password_here** with my actual database details.

![46](img/46.png)

- I modified the configuration file projectlamp.conf: **`sudo nano /etc/apache2/sites-available/projectlamp.conf`** to update the document root to the directory where my WordPress installation is located.

![47](img/47.png)

- After updating the document root to **`/var/www/html`** directory in my editor, I saved the changes and exited.

![48](img/48.png)

- I reloaded Apache for the changes to take effect: **`sudo systemctl reload apache2`**.

- After I've completed these steps, I then access my WordPress page to complete the installation. I opened my web browser and typed  **`http://<EC2 IP>/wordpress/`**. This led me to the WordPress setup wizard where I finalized the installation process.

> [!NOTE]
I replaced **<EC2 IP>** with the IP address of my EC2 instance when accessing my WordPress page.

- I selected my preferred language and then clicked on **Continue** to proceed.

![49](img/49.png)

- I entered the required information and clicked on **Install WordPress** when I have finished.

  - **Site Title①:** I entered the name of my WordPress website. It's recommended to use domain name for better optimization.
  - **Username②:** I chose a username for logging into WordPress.
  - **Password③:** I set a secure password to protect my WordPress account.
  - **Your email④:** I provided my email address to receive updates and notifications.
  - **Search engine visibility⑤:** I left this box unchecked to prevent search engines from indexing my site until it's ready.

![50](img/50.png)

- WordPress has been successfully installed. I now logged in to my admin dashboard using the previously set up information by clicking the **Log In** button.

![51](img/51.png)

- I entered my username and password, then clicked **Log In** to access my WordPress admin dashboard.

![52](img/52.png)

- After I'm successfully logged in, I was greeted by the WordPress dashboard page.

![53](img/53.png)

---

### Create An A Record

To make my website accessible via my domain name rather than the IP address, I'll need to set up a DNS record. I did this by buying my domain from Qservers and then moving hosting to AWS Route 53, where I set up an A record.

> [!NOTE]
Visit [**Project1**](https://github.com/StrangeJay/devops-beginner-bootcamp/blob/main/project1/project1.md) for instructions on how to create a hosted zone.

- I pointed my domain's DNS records to the IP addresses of my Apache load balancer server.

- In route 53, I clicked on **Create record**.

![54](img/54.png)

- I pasted my **IP address➀** and then clicked on **Create records➁** to create the root domain.

![55](img/55.png)

- I clicked on **Create record** again, to create the record for my sub domain.

![56](img/56.png)

- I pasted my IP address➀, input the Record name(**www➁**) and then clicked on **Create records**➂.

![57](img/57.png)

- To update my Apache configuration file in the sites-available directory to point to my domain name, I used the command: **`sudo nano /etc/apache2/sites-available/projectlamp.conf`**.

> [!NOTE]
This command opens the **`projectlamp.conf`** file in the nano text editor with superuser privileges **(`sudo`)**. Within the editor, I adjusted the necessary details to reflect my domain name configuration.

- I ensured that the server settings in my Apache configuration point to my domain name, and that the document root accurately points to my WordPress directory. After I've made these adjustments, I saved the changes and exited the editor.

```
<VirtualHost *:80>
    ServerName <Your root domain name>
    ServerAlias <Your sub domain name>
    ServerAdmin webmaster@<Your root domain name>

    DocumentRoot /var/www/html/wordpress

    <Directory /var/www/html/wordpress>
        Options Indexes FollowSymLinks
       # AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

```

![58](img/58.png)

> [!NOTE]
The new configuration defines how Apache should handle requests for my domain, and its subdomain. With this configuration: Apache will handle requests for **timtanium.name.ng** and **www.timtaium.name.ng**. Files will be served from the **`/var/www/html/wordpress`** directory. Directory listings and symbolic links are allowed. The directory can be accessed by any client.
Error logs will be written to **`/var/log/apache2/error.log`**. Access logs will be written to **`/var/log/apache2/access.log`** in the combined log format.

- To update my **`wp-config.php`** file with DNS settings,I used the following command: **`sudo nano wp-config.php`** and added these lines to the file:

```
/** MY DNS SETTINGS */
define('WP_HOME', 'http://<domain name>');

define('WP_SITEURL', 'http://<domain name>');
```

I replaced **`http://<domain name>`** with my actual domain name. Saved the changes and exited the editor.

![59](img/59.png)

- I reloaded my Apache server to apply the changes with the command: **`sudo systemctl reload apache2`**, After reloading, I visited my website at **`http://<domain name>`** to view my WordPress site. I replaced **<domain name>** with my actual domain name.

![60](img/60.png)

- To log in to my WordPress admin portal, I visited **`http://<domain name>/wp-admin`**, I entered my **username①** and **password②**, then clicked on **log In③**. *I replaced **<domain name>** with my actual domain name.*

![61](img/61.png)

> [!NOTE]
My domain name is **timtanium.name.ng**, so i'll visit **`http://timtanium.name.ng/wp-admin`**.

- Now that my WordPress site is successfully configured to use my domain name, the next step is to secure it by requesting an SSL/TLS certificate.

![62](img/62.png)

---

### Install certbot and Request For an SSL/TLS Certificate

- I installed certbot by executing the following commands:
`sudo apt update`
`sudo apt install certbot python3-certbot-apache`

- I ran the command **`sudo certbot --apache`** to request my SSL/TLS certificate. I followed the instructions provided by Certbot to select the domain name for which I want to enable HTTPS.

![63](img/63.png)

- I  received a message confirming that the certificate has been successfully obtained.

![64](img/64.png)

- I visited my website to confirm, and I noticed that the **"not secure"** warning no longer appears, indicating that my site is now secure with HTTPS.

![105](img/Screenshot%20(105).png)

---
---

#### The End Of Project 4
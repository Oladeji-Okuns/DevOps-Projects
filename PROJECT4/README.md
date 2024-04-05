# WEB STACK IMPLEMENTATION (LEMP STACK)

## OBJECTIVE

The purpose of this project is to deploy a LEMP Stack website successfully in AWS Cloud.

## LEARNING OUTCOME

To build a foundation for serving PHP websites and applications to website visitors, using Nginx as a web server and MySQL as a database management system.

## INTRODUCTION

The LEMP Stack, similar to the LAMP Stack, is also a combination of four different technologies which is used for websites and web applications, but unlike the LAMP Stack, the difference here with the LEMP Stack is that it makes use of Nginix as the webserver instead of Apache which is used in LAMP.

For LEMP Stack:
- Operating system      -   Linux           (L)
- Webserver             -   Nginix/EngineX  (E)
- Database Server       -   MySQL           (M)
- Programming Language  -   PHP             (P)

### WHAT IS NGINX?

Nginx is a free open-source web server that can also be used as a reverse proxy, load balancer, mail proxy, and HTTP cache. It is known for its high performance, stability and low resource usage.



## PREREQUISITES

- Knowledge of Linux commands and operations.
- Provisioned EC2 Instance with Ubuntu Server OS running.

## INSTALLING THE NGINX WEB SERVER

Web servers are important in order to display web pages to our site's visitors, and in this project we will make use of Nginx, a high-performance web server.

In order to install this web server, we will use the `apt` package manager to install this package.

First, we update our package index, then we go ahead to get Nginx installed using the `apt install` command as shown below:

`sudo apt update`

![alt text](apt_update.png)


`sudo apt install nginx`

![alt text](nginx_installed.png)


To verify the installation of nginx was successfully done and to confirm it is up and running, we use the command below:

`sudo systemctl status nginx`

![alt text](nginx_install_verified.png)

Once this command is run, if the output shows that it is active (running) and in green color as shown above, then you did every thing correctly and have launched the web server in the cloud.

### Setting In-bound Rules

The default port that web browsers use to access web pages on the internet is TCP port 80, so we need to open this port before we can receive any traffic by our web server.

By default, TCP port 22 is open by default on our EC2 machine to enable us access it via SSH, hence we need to add a rule to our EC2 configuration to open in-bound connection through port 80.

The steps to setting/editing the in-bound rules to enable in-bound connection via port 80 are shown below:

![alt text](setting_inbound1.png)

![alt text](setting_inbound2.png)

![alt text](setting_inbound3.png)

![alt text](setting_inbound4.png)

![alt text](setting_inbound5.png)

![alt text](save_inbound_rule.png)


Now our server is running and we can access it locally and from the internet.

**NOTE**: The setting with Source 0.0.0.0/0 means the server can be accessed from any IP address.

### Accessing the server locally:

We can check how we can access the web server locally in our Ubuntu shell by running the command below:

- By DNS name:

`curl http://localhost:80`

![alt text](accessing_nginx_locally.png)

OR 

- By running the command below using the IP Address:

`curl http://127.0.0.1:80`

![alt text](access_nginx_locally_by_IP.png)

In the above, we used the `curl` command to request our Nginx on port 80, although it would still work even if the port number is not specified.
The IP address `127.0.0.1` corresponds to the DNS Name `localhost`, so whichever one is used, it would give the same result as the DNS name is usually resolved to its corresponding IP address.

**NOTE:** The process of converting a DNS name to IP address is called **Resolution**.

### Testing how our Nginx Server responds to requests on the internet

We can check or test how our Nginx server responds to requests by following the steps below:

- Open a web browser of your choice (in this case 'Chrome Browser')
- Access the webserver using the following URL below:

`http://<Public-IP-Address>:80`

In this case, we insert the public IP address of the server.

`http://13.60.7.254:80`

![alt text](Confirming_Nginx_on_browser.png)


### Retrieving Public IP-Address

You can get your server's public IP address by using two methods:

- Log into your AWS concole, get to EC2 instances running, select the required server and navigate to the `details` section on the AWS web console to find the Public IP-address of your server.

- Another method of retrieving your public IP address is by using the following command below:

`curl -s http://169.254.169.254/latest/meta-data/public-ipv4`

![alt text](Retrieving_pubic_IP_address_via_command.png)


## INSTALLING MySQL

The next step after having our web server up and running is to install a Database Management System (DBMS) which will enable us to be able to store and manage data for our site in a relational database.
For the purpose of this project, we will use MySQL which is a popular relational database management system used within PHP environments.

As usual, in order to acquire and install the DBMS, we need to use the `apt` command, as shown below:

`sudo apt install mysql-server`

![alt text](Installing_mysql.png)

You will get a prompt requiring you to input 'Y' to confirm installation, do this and press`Enter` button to proceed.

Once the installation is finished, we can then log in to the MySQL console by typing:

`sudo mysql`

![alt text](logging_into_mysql.png)

Running this command will connect you to the MySQL server as the administrative database user **root**, which is inferred by the use of the 'sudo' when running this command.

**NOTE:** As an important recommendation, we need to run a security script that comes pre-installed with MySQL to remove some insecure default settings and lockdown access to our database system.

Before running this script, we have to set a password for the **root** user, using mysql_native_password as default authentication method. For this user, we will define the password as `PassWord.1`.

To set this password, we enter the code below:

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

![alt text](setting_mysql_password.png)

After this, we can now exit the MySQL shell with the command below:

`exit`

![alt text](exiting_mysql_server.png)

### Runnning the Security Script

We can now start the interactive script by running the command below:

`sudo mysql_secure_installation`

![alt text](run_mysql_sec_script.png)


Once this command is run, you will be asked if you want to configure the `VALIDATE PASSWORD PLUGIN`.

**NOTE:** Choosing to enable this feature is more of a choice or judgement call. If you choose to enable it, then passwords which do not match the specified criteria will be rejected by MySQL with an error. It is safe to leave validation disabled, but you should always use strong, unique passwords for database credentials.

Also note that there are three levels of password validation policy as shown in the screenshot above. Select the policy which is preferrable to you but remember that if you select and enter `2`, which is for the strongest level, you will receive errors when attempting to set any passwords that do not meet its stated criteria.

Whether you choose to set up the `VALIDATE PASSWORD PLUGIN` or not, your server will ask you to select and confirm a password for the MySQL **root** user which is not to be confused with the **system root**. It is worthy of note that the **database root user** is an administrative user with full privileges over the database system. It is recommended to define a strong password here.

If password validation was enabled, the password strength of the root password will be shown and you will have the option to continue with that password by entering `Y` for `yes` at the prompt.

After this, answer `Y` as in `yes` for anything else. You will then be prompted to change the root password, remove anonymous users and test database, disable remote root logins, and load these new rules so that MySQL immediately respects the changes you have made.

### MYSQL Login Test

After completing the steps above, it is time to test if we are able to log in to the MySQL console. We can do this by typing in the following command below:

`sudo mysql -p`

**NOTE:** In the command above, there is a `-p` flag, and its purpose is to prompt you for the password used after changing the **root password**.

![alt text](mysql_login_prompt.png)


### Exiting the MYSQL

We can exit the MySQL console by typing the command below:

`exit`

![alt text](exiting_mysql.png)



Take note that you will need to provide a password for you to be able to connect as the **root user**. For security reasons, it is better to have dedicated user accounts with less expansive privileges set up for every database, especially if you plan on having multiple databases hosted on your server.

### Checking the current version of MySQL Server

You can check what version of your MySQL server is running by using the command below:

`sudo mysql -p`

![alt text](checking_mysql_version.png)


With these above, the MySQL server is now installed and secured. Next, we will go into installing PHP which is the final component in the LEMP stack.


## INSTALLING PHP

As a recap, we have already installed Nginx in order to serve our content and we have also installed MySQL to store and manage our data. Now, we can install PHP to process code and generate dynamic content for the web server.

Unlike in the case of Apache which embeds the PHP interpreter in each request, Nginx requires an external program to handle PHP processing and act as a link or bridge between the PHP interpreter itself and the web server.

The advantage of this is that it allows for a better overall performance in most PHP-based websites, but the CON is that it requires additional configuration.

### Requirements to install PHP

To install our PHP, we need two packages:

- First we need to install `php-fpm`, which stands for **"PHP FastCGI Process Manager"**, and then, we tell or instruct Nginx to pass PHP requests to this software for processing.

- Secondly, we need to install `php-mysql`, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

It is possible to install these two packages at once. To do so, we run the following command below:

`sudo apt install php-fpm php-mysql`

When prompted, type `Y` and press `Enter` to confirm installation.

![alt text](installing_PHP_packages.png)

![alt text](PHP_installed.png)


## CONFIGURING NGINX TO USE PHP PROCESSOR

An interesting thing about Nginx web server is that we can create server blocks (similar to Virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server.

For the purpose of this project, we will use **projectLEMP** as a sample domain name.

By default, Ubuntu has one server block enabled and configured to serve documents out of a directory at `/var/www/html`. This default setting works well for a single site but becomes difficult to manage if we intend to host multiple sites.

Therefore, instead of modifying `/var/www/html/`, we will create a directory structure within `/var/www/` for our chosen domain website (**projectLEMP**), leaving `/var/www/html/` in place as the default directory to be served if a client's research do not match any other sites.

### Creating root web directory for domain

To create the root web directory for our chosen domain **projectLEMP**, we do the following:

`sudo mkdir /var/www/projectLEMP`

![alt text](creating_rootweb_dir.png)

### Assigning Directory Ownership

Next step is to assign ownership of the directory with the $USER environment variable, which will reference our current system user:

`sudo chown -R $USER:$USER /var/www/projectLEMP`

![alt text](assigning_dir_ownership.png)

### Verifying Directory Ownership

![alt text](verifying_dir_ownership.png)


### Setting up new configuration file in Nginx's `sites-available`

Next thing to do is to open a new configuration file in Nginx's `sites-available` directory using our preferred command-line editor. In this case, we will be using `nano` editor:

The following script is inserted into the config file:

    #/etc/nginx/sites-available/projectLEMP

    server {
        listen 80;
        server_name projectLEMP www.projectLEMP;
        root /var/www/projectLEMP;

        index index.html index.htm index.php;

        location / {
            try_files $uri $uri/ =404;
        }

        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        }

        location ~ /\.ht {
            deny all;
        }

    }


![alt text](config_script_sites_available.png)

![alt text](sites-available_configscript.png)


#### Function of each of the directives and location block:

Below is an outline of what the directives and location blocks inserted into the configuration file do:

- `listen` - This directive defines the port which Nginx will listen on. In the case of this project, the Nginx web server will listen on port `80`, which is the default port for HTTP.

- `root` - This defines the document root where the files served by this website are stored.

- `index` - This defines the order in which Nginx will prioritize index files for this website. As a common practice or standard, `index.html` files are usually listed with higher precedence than the `index.php` files to allow for quickly setting up a maintenance landing page in PHP applications. These settings can be adjusted to better suit our application needs.

- `server_name` - This defines the domain names and/or IP addresses this server block will should respond for. **We must point this directive to our server's domain or public IP address**

- `location /` - This is used to define configuration settings for the root of the server. This means that it applies to requests that match the root location, which is typically just the domain name (e.g., google.com)

In this case of the project, the first location block includes a `try_files` directive, which checks for the existence of files or directories matching a URI request. If Nginx cannot find the appropriate resource, it will return a 404 error.

- `location ~ \.php$` - This location block handles the actual PHP processing by pointing Nginx to fastcgi-php.conf configuration file and the `php7.4-fpm.sock file`, which declares what socket is associated with `php-fpm`.

- `location ~ /\.ht` - This is the last location block and it deals with `.htaccess` files, which Nginx does not process. By adding the deny all directive, if any `.htaccess` files happen to find their way into the document root, they will not be served to visitors.

After we are done editing, we can save and close the file. Since we are using `nano` in this case, all we need to do is type `CTRL+X` and then `Y` and finally press `ENTER` button to confirm.

### Activating the Configuration

Next step is to activate our configuration by linking to the config file from Nginx's `sites-enabled` directory.
We can do this by running the command below:

`$ sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`

![alt text](activating_config.png)

This command above which we ran will tell Nginx to use the new configuration next time it is reloaded.

### Testing the Configuration

We can test our configuration for syntax errors by typing and entering the following command below:

`sudo nginx -t`

The output of running this command is shown in the image below:

![alt text](config_test.png)


**NOTE:** If errors are reported after running the command to test for syntax errors, then you have to go back to your configuration file to review its contents before continuing.


### Disabling Default Nginx Host

We need to disable the default Nginx host which is currently configured to listen on port 80. To do this, we run the command below:

`sudo unlink /etc/nginx/sites-enabled/default`

![alt text](defaultnginxhost_disabled.png)

### Reloading Nginx

After disabling the default Nginx host, the next thing we must do is to reload Nginx in order to apply all the changes we have made. We reload Nginx using the command below:

`sudo systemctl reload nginx`

![alt text](nginx_reload.png)

Having done all this, our new website is now ready, although the web root /var/www/projectLEMP is still empty. Therefore, we need to create an index.html file in that location so that we can test that our new server block works as expected.

### Creating index.html file

To create an index.html file to test that our server block works well, we do this by running the command below:

`sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`

![alt text](testindex.html_created.png)

### Testing Our Website via IP Address

Now, we can go to our browser and try to open our website URL using the IP address.

`http://<Public-IP-Address>:80`

In this case the Public IP address is given as shown below:

`http://13.60.7.254:80`

![alt text](test_website.png)

In the image above, we are able to see the text from the **'echo'** command we wrote into the index.html file, this means that our Nginx site is working as expected.

### Testing our Website via Domain Name

Apart from using our IP address, we can also access our website in our browser by public DNS name and the result would be the same. Including the port number is optional.
We enter the following in our browser to access our website:

`http://<Public-DNS-Name>:80` 

In this case, the Public DNS name is given below:


`http://ec2-13-60-7-254.eu-north-1.compute.amazonaws.com:80`

![alt text](Test_landingpage.png)

We can leave this file in place as a temporary landing page for our application until we set up an `index.php` file to replace it. Once we get that done, we need to remember to remove or rename this `index.html` file from our document root as it would take precedence over the `index.php` file by default.

With these done, our LEMP Stack is now fully configured and the next step will be for us to create a PHP script to test that our Nginx is actually able to handle `.php` files within our newly configured website.


## TESTING PHP WITH NGINX

As stated above, our LEMP Stack is completely set up and operational and we can test it to validate that Nginx can correctly hand `.php` files off to our PHP processor. 

We can do this by:

- **Creating a test PHP file in our document root:** 

    We open a new file called `info.php` within the document root in our text editor. We do this by entering the command below:

    `nano /var/www/projectLEMP/info.php`

    ![alt text](creating_phpfile.png)

- **Entering PHP code into the PHP file:**

    After creating and opening the `info.php` file, we can then type or paste the following code below into it. This code is actually a valid PHP code that will return information about our server.

    `<?php `

    `phpinfo();`

    ![alt text](php_code.png)

- **Website test:**
    
    After doing all these above, we can now access this page on our web browser by visiting the domain name or public IP address we have set up in our Nginx configuration file followed by `/info.php`:

    It should follow the format below:

    `http://'server_domain_or_IP'/info.php`

    In the case of this project, the actual URI will be as shown below:

    1. **Through IP Address:**
    
        `http://13.60.7.254/info.php`
    
        ![alt text](phpinfo_test_via_IP.png)


    2. **Through DNS:**

        `http://ec2-13-60-7-254.eu-north-1.compute.amazonaws.com//info.php`

       ![alt text](phpinfo_test_via_DNS.png) 


- **Deleting the test file:**

    Once we are done checking the relevant information about our PHP server through the page above, it is recommended to remove this file we created as it contains sensitive information about our PHP environment and our Ubuntu server.

    To remove this file we use the `rm` command as shown below:

    `sudo rm /var/www/projectLEMP/info.PHP`

    ![alt text](removing_phpfile.png)

    
    
    To confirm that the PHP file has been deleted, we run:

    `sudo cat /var/www/projectLEMP/info.php`

    ![alt text](confirming_phpfile_removed.png)


    We can always regenerate this file if we need it later.



## RETRIEVING DATA FROM MYSQL DATABASE WITH PHP

In this stage, we will create a test database (DB) with a simple "To do" list and configure access to it so that the Nginx website would be able to query data from the DB and display it.
At the time of writing this project, the native MYSQL PHP Library `mysqlnd` does not support `caching_sha2_authentication` which is the default authentication method for MYSQL8.

Therefore, in order to be able to connect to the MYSQL database  from PHP, we will need to create a new user with the `mysql_native_password` authentication method.

### Creating Database and Database User:

For the purpose of this project, we will create a database named **example_database** and a user named **example_user**. The following steps below were followed:

1. **Connect to MYSQL Console:**

    Connected to the MySQL console using the **root** account. This was done by entering the command below:

    `sudo mysql`

    ![alt text](accessing_mysql.png)


2. **Create new Database:**

    Ran the command below to create a new database from our MySQL console 

    `CREATE DATABASE 'example_database';`

    ![alt text](create_newDB.png)


3. **Create new user:**

    Now, we can create a new user and grant him full privileges on this database we have just created.

    To do this, we run the following command to create the new user named `example_user`, using mysql_native_password as the default authentication method. A unique password must be set for this user, in this case we will define PassWord.1 as the password.

    `CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

    ![alt text](create_user.png)

### Grant Permissions:

At this point, we now have to give this new user permission over the `example_database`, and we do this by running the command below:

`GRANT ALL ON example_database.* TO 'example_user'@'%';`

![alt text](granting_permissions.png)


With this, we have given the user named **`example_user`** full privileges over the **`example_database`** database, while preventing the user from creating or modifying other databases on our server.


### Exit MySQL:

Now we can exit the MySQL shell using the `exit` command.

![alt text](exiting_mysql1.png)


### Testing permissions of the New User:

We can test if the new user has the proper permissions by logging in to the MySQL console again, but this time, using the custom user credentials, as shown below:

`mysql -u example_user -p`

![alt text](mysqluser_test.png)


**NOTE:** The `-p` flag in this command helps to prompt us for the password we used when creating the `example_user` user. After logging in to the MySQL console, we then confirm that we have access to the `example_database` database. We run the command below to display the list of databases available:

`SHOW DATABASES;`

![alt text](show_db.png)


### Create Test Table:

Now, we will create a test table named **todo_list**, and to get this done, we will run the following statement from the MySQL console:

`CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id));`

![alt text](creating_test_table.png)


### Insert content into test table:

Next, we can insert a few rows of content into the test table. To create more content, we can repeat the command below a number of times but with different values to show different content being inserted:

`INSERT INTO example_database.todo_list (content) VALUES ("My first important item");`

![alt text](insert_content_into_test_table.png)


### Confirm Data in Table:

To confirm that the data was successfully saved to our table, we run the command below:

`SELECT * FROM example_database.todo_list;`

![alt text](confirm_content_in_DB.png)


After confirming we have valid data in the test table, we can now exit the MySQL console with the `exit` command.



### Creating PHP Script to connect to MySQL:

At this point, we can then create a PHP script to connect to MySQL and query for our content.

To do this, we create a new PHP file in our custom web root directory using our preferred editor. In this case, we will use nano editor following the statement below.

`nano /var/www/projectLEMP/todo_list.php`

![alt text](creating_phpscrpt_via_nano_editor.png)


Then we input or insert the PHP script into the file via the nano editor as shown below:

    <?php
    $user = "example_user";
    $password = "PassWord.1";
    $database = "example_database";
    $table = "todo_list";

    try {
      $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
      echo "<h2>TODO</h2><ol>";
      foreach($db->query("SELECT content FROM $table") as $row) {
        echo "<li>" . $row['content'] . "</li>";
      }
      echo "</ol>";
    } catch (PDOException $e) {
        print "Error!: " . $e->getMessage() . "<br/>";
        die();
    }

![alt text](inserting_PHP_script.png)


With this done, we can now save and close the file.

Finally, we can now access this page in our web browser by visiting the domain name or public IP address configured for our website followed by `/todo_list.php`, as shown below:


`http://<Public_domain_or_IP>/todo_list.php`

In the case of this project, it is given as below:

`http://13.60.7.254/todo_list.php`


![alt text](confirming_DBdata_via_IP.png)


OR

`http://ec2-13-60-7-254.eu-north-1.compute.amazonaws.com/todo_list.php`


![alt text](confirming_DBdata_via_DNS.png)





**End of LEMP Stack Project**






























































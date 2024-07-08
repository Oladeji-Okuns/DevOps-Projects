# AUTOMATING LOAD BALANCER CONFIGURATION WITH SHELL SCRIPTING
## OBJECTIVE:
The purpose of this project is to learn, understand and demonstrate how to automate the setup and maintenance our our load balancer using a freestyle job while enhancing efficiency and reducing manual effort.

## PRE-REQUISITES:
- Basic knowledge of Linux commands
- Provisioning EC2 instances on AWS
- Basic Shell Scripting knowledge
- Good knowledge of Client and Server systems


## INTRODUCTION
Automating Load balancer Configuration With Shell Scripting simply refers to the process of using shell scripts to automate the setup and management of load balancers in a computing environment.<br>
As Devops Engineers, automation is at the heart of the work we do as it helps us speed up the deployment of services and reduces the chances of making errors in our day to day activities.


## AUTOMATING THE DEPLOYMENT WEBSERVERS
In this project, we will be automating the deployment and configuration of two backend servers, with a load balcncer distributing traffic across the webservers.<br>
We will do this by writing a shell script, such that when it is run, it will carryout the deployment and configuration activity automatically for us.


## DEPLOYING AND CONFIGURING THE WEBSERVERS
In order to do this, first we need to create a script in which all the process needed to deploy our webservers has been codified into it, as shown below:

    #!/bin/bash

    ####################################################################################################################
    ##### This automates the installation and configuring of apache webserver to listen on port 8000
    ##### Usage: Call the script and pass in the Public_IP of your EC2 instance as the first argument as shown below:
    ######## ./install_configure_apache.sh 127.0.0.1
    ####################################################################################################################
    
    set -x # debug mode
    set -e # exit the script if there is an error
    set -o pipefail # exit the script when there is a pipe failure

    PUBLIC_IP=$1

    [ -z "${PUBLIC_IP}" ] && echo "Please pass the public IP of your EC2 instance as an argument to the script" && exit 1

    sudo apt update -y &&  sudo apt install apache2 -y

    sudo systemctl status apache2

    if [[ $? -eq 0 ]]; then
        sudo chmod 777 /etc/apache2/ports.conf
        echo "Listen 8000" >> /etc/apache2/ports.conf
        sudo chmod 777 -R /etc/apache2/

        sudo sed -i 's/<VirtualHost \*:80>/<VirtualHost *:8000>/' /etc/apache2/sites-available/000-default.conf

    fi
    sudo chmod 777 -R /var/www/
    echo "<!DOCTYPE html>
            <html>
            <head>
                <title>My EC2 Instance</title>
            </head>
            <body>
                <h1>Welcome to my EC2 instance</h1>
                <p>Public IP: "${PUBLIC_IP}"</p>
            </body>
            </html>" > /var/www/html/index.html

    sudo systemctl restart apache2

After getting this script prepared, we need to follow the steps below inorder to run the script:

### **Step 1:** Provision two EC2 instances running Ubuntu 24.04"

Two EC2 instances running ubuntu 24.04 were created and named as **`Lag_server1`** and **`Lag_server2`**, as shown below:

![alt text](images/2_EC2_inst_creation.png) 

![alt text](images/2_instances_named.png)

    NOTE: Initially the servers were not named until after the instances were created, and this is because two EC2 instances were being created at the same time. Also bear in mind that you have to either use an existing key_pair or create a new on if you want to.

### **Step 2:** Port configuration and setting inbound rules:
Next, we have to set the security group's inbound rules and open port 8000 and allow traffic from anywhere for the two EC2 instances meant for the servers.  

**For Lag_Server1:**

![alt text](images/set_inboundrule.png)

![alt text](images/port8000_opened.png)

![alt text](images/port_configured.png)


**For Lag_Server2:**

Since both servers were created at the same time and with the same security group, then automatically, `Lag_server2` inherited the port 8000 configuration from the common security group with `Lag_server1` as shown below:

![alt text](images/serv2_port_config.png)



### **Step 3:** Connect to the webserver through the SSH client:

Select the server you wish to SSH into, then click on the `connect` tab to get the link required to ssh into the server, as shown below:

**For Lag_Server1**

![alt text](images/connect_serv1.png)

![alt text](images/ssh_serv1_connection.png)


**For Lag_Server2**

![alt text](images/ssh_details_serv2.png)

![alt text](images/connecting_to_serv2.png)

![alt text](images/ssh_connection_to_serv2.png)


### **Step 4:** Open a file and enter the texts in the script above into the file,  or simply paste the script above into the file.

Do this by running the command below on the terminal, as shown below:

`sudo vi install.sh`

**For Lag_server1:**

![alt text](images/open_install.sh_file.png)

Then, paste or enter the script texts into the file, as shown below:

![alt text](images/paste_script.png)

After pasting or entering the script, close the file by pressing the **`esc`** key or button, and then the **`Shift`** + **`wqa!`**.


**For Lag_server2:**

![alt text](images/creating_install_file_for_serv2.png)

![alt text](images/install_script_entered_serv2.png)


### **Step 5:** Change permissions: 

Change the permissions on the file to make it an executable file using the command below:

**On Lag_server1:**

`sudo chmod +x install.sh`

![alt text](images/permissions_changed.png)


**On Lag_server2:**

![alt text](images/serv2_install_file_permissions_changed.png)


### **Step 6:** Now, we can run the shell script using the command below. It is also important that we read the instructions in the shell script to leaern how to use it properly.


`./install.sh PUBLIC_IP`

This command above simply means that we have to call the script with the `./install.sh` which indicates that the file `install.sh is being called and the `./` indicates that the script is being called from the current directory whcih is where the script exists.<br>
Then, it is followed by the `PUBLIC_IP` which should be the actual public IP address of the server's EC2 instance.

Before running this command, we have to obtain the public IPs for the two servers before calling the script with the IPs also included, as shown below:

**On Lag_server1:**

Obtain public IP from AWS EC2 instance details:

![alt text](images/serv1_public_IP.png)

**Running the command:**

`./install.sh 16.170.235.97`

![alt text](images/running_install_script_on_serv1.png)

**Browser Test:**

![alt text](images/serv1_browser_test.png)



**On Lag_server2:**

![alt text](images/serv2_public_IP.png)

**Running the command:**

`./install.sh 13.48.193.124`

![alt text](images/running_install_script_onserv2.png)

**Browser Test:**

![alt text](images/serv2_browser_tes.png)

<br>
<br>

## DEPLOYMENT OF NGINX AS A LOAD BALANCER USING SHELL SCRIPT

Since we have successfully deployed and configured the two webservers, next thing to do is to automate the deployment of Nginx as a Load Balancer using shell script.<br>
To do this we need to follow the steps below:

**Step 1:** Provision an EC2 instance for the load balancer.

On the EC2 instances page, click on `Launch instance`, then go ahead to provision the EC2 instance for the load balancer with ubuntu 24.04, as shown below:

![alt text](images/EC2_for_load_bal_created.png)

![alt text](images/lag_loadbal_created.png)


**Step 2:** Configuring Inbound rule and opening port 80 on load balancer.

![alt text](images/setting_inboundrule_loadbal.png)

![alt text](images/opening_port80_loadbal.png)

![alt text](images/loadbal_inboundrule_configured.png)


**Step 3:** Connecting to the Load Balancer via SSH.

![alt text](images/connecting_to_loadbal.png)

![alt text](images/ssh_details_load_bal.png)

![alt text](images/ssh_connection_to_load_bal.png)



**Step 4:** Next, we have to create a shell script that has been codified with all the steps that will handle the automation of the deployment and configuration of the load balancer with Nginx as shown in the script below:


    #!/bin/bash

    ######################################################################################################################
    ##### This automates the configuration of Nginx to act as a load balancer
    ##### Usage: The script is called with 3 command line arguments. The public IP of the EC2 instance where Nginx is installed
    ##### the webserver urls for which the load balancer distributes traffic. An example of how to call the script is shown below:
    ##### ./configure_nginx_loadbalancer.sh PUBLIC_IP Webserver-1 Webserver-2
    #####  ./configure_nginx_loadbalancer.sh 127.0.0.1 192.2.4.6:8000  192.32.5.8:8000
    ############################################################################################################# 

    PUBLIC_IP=$1
    firstWebserver=$2
    secondWebserver=$3

    [ -z "${PUBLIC_IP}" ] && echo "Please pass the Public IP of your EC2 instance as the argument to the script" && exit 1

    [ -z "${firstWebserver}" ] && echo "Please pass the Public IP together with its port number in this format: 127.0.0.1:8000 as the second argument to the script" && exit 1

    [ -z "${secondWebserver}" ] && echo "Please pass the Public IP together with its port number in this format: 127.0.0.1:8000 as the third argument to the script" && exit 1

    set -x # debug mode
    set -e # exit the script if there is an error
    set -o pipefail # exit the script when there is a pipe failure


    sudo apt update -y && sudo apt install nginx -y
    sudo systemctl status nginx

    if [[ $? -eq 0 ]]; then
        sudo touch /etc/nginx/conf.d/loadbalancer.conf

        sudo chmod 777 /etc/nginx/conf.d/loadbalancer.conf
        sudo chmod 777 -R /etc/nginx/


        echo " upstream backend_servers {

                # your are to replace the public IP and Port to that of your webservers
                server  "${firstWebserver}"; # public IP and port for webserser 1
                server "${secondWebserver}"; # public IP and port for webserver 2

                }

            server {
                listen 80;
                server_name "${PUBLIC_IP}";

                location / {
                    proxy_pass http://backend_servers;   
                }
        } " > /etc/nginx/conf.d/loadbalancer.conf
    fi

    sudo nginx -t

    sudo systemctl restart nginx


**Step 5:** Running the Shell Script:

To run the created shell script, the following steps have to be followed:

- On the terminal for the load balancer, open a file named **`nginx.sh`** using the command below:

`sudo vi nginx.sh`


- The, copy and paste the script above into the file as shown below:

![alt text](images/pasting_nginx.sh_script.png)

![alt text](images/view_created_nginx_script.png)


- Close the file by pressing the **`esc`** key, then press the **`Shift key`** + `:wqa!`

- Next, change the file permissions for the `nginx.sh` file to make it an executable file using the command below:

`sudo chmod +x nginx.sh`

![alt text](images/change_permissions_for_nginx_file.png)


- After all these steps above have been, now we can run the script using the command syntax below:

`./nginx.sh PUBLIC_IP Webserver-1 Webserver-2`

This command above requires that we put in the actual public IP address of the load balancer from the EC2 details, and then we need to replace Webserver-1 and Webserver-2 with the URLs/IP addresses of the 2 webservers created, as shown below:

`./nginx.sh 16.170.251.80 16.170.235.97:8000 13.48.193.124:8000`

![alt text](images/nginx_script_run.png)



## VERIFYING THE SETUP

We verify the whole setup by going to our web browser and then typing in our Nginx load balancer IP address to see if we will be served with pages from our two webservers as we refresh the browser, as shown below:

![alt text](images/server1_served.png)

![alt text](images/server2_served.png)



**END OF PROJECT 8**
















































































































































































































































































































































































































































































































































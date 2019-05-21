# Full Stack Web Developer Nanodegree

<h1> 
<a href="https://www.udacity.com/">
  <img src="https://s3-us-west-1.amazonaws.com/udacity-content/rebrand/svg/logo.min.svg" width="300" alt="Udacity logo">
  
</a> &nbsp; 
Project: Linux Server Configuration
</h1>

## Table of Contents

- [Full Stack Web Developer Nanodegree](#full-stack-web-developer-nanodegree)
  - [Table of Contents](#table-of-contents)
  - [Intro](#intro)
  - [Access the application](#access-the-application)
    - [Accessing via ssh port](#accessing-via-ssh-port)
  - [Setup Linux Server Instance](#setup-linux-server-instance)
    - [Firewall setup](#firewall-setup)
    - [DNS Setup using AWS Route53](#dns-setup-using-aws-route53)
    - [Summary of installed software](#summary-of-installed-software)
    - [Python3 installation and configuration](#python3-installation-and-configuration)
    - [Apache2 installation and configuration](#apache2-installation-and-configuration)
    - [Postgresql installation and configuration](#postgresql-installation-and-configuration)
  - [Linux User Management](#linux-user-management)
    - [Requirements](#requirements)
  - [Security](#security)
    - [The files for catalog application](#the-files-for-catalog-application)
    - [Accessing the application](#accessing-the-application)

## Intro

Host the catalog web application in a base lined Linux Server. Application primarily uses python via a virtual environment. Application is primarily rendered in Apache using wsgi module. Also this project is to secure the server from attackers and to configure the application and database server. This project will enable to access and secure the [Amazon Lightsail](www.mycatalogapp.net) Ubuntu Linux server instance and install a web server and database server hosted in Lightsail. Additionally, the server is setup to have a DNS to use the google oauth functionality.

## Access the application

Custom Catalog [application](www.mycatalogapp.net) is hosted in apache web server using the web service gateway interface.  

### Accessing via ssh port

Server can be accessed using `grader`. This user has been setup to access the server using ssh, key separately shared. Additionally, grader is givern sudo access to run specific sudo commands.

Server IP: 3.15.73.237

ssh port: 2200

## Setup Linux Server Instance

A new Linux server instance is created in Amazon lightsail using Ubuntu as the core OS. Default user "ubuntu" is used to perform the basic server setups. The Public access to server is disabled and server can be accessed only via custom port. Server is setup by running the update first and then upgrade
`sudo apt-get update`

`sudo apt-get upgrade`

Server is configured to use the `UTC` local timezone.

### Firewall setup

ufw tool is used to setup the required firewall setups in Ubuntu. Connections for ssh, TCP, HTTP, NTP are allowed. Default ssh port is changed as per project requirements. 

To check the available firewall setup run the below command

`sudo ufw status verbose`

### DNS Setup using AWS Route53

[AWS Route53](https://aws.amazon.com/route53/) is used to setup the DNS for the server. Additionally the AWS certificate manager is used to install and manage the ssl certificate for the lightsail server. Amamzon Certificate Manager (ACM) is used to link the certificate with Route53 DNS which is attached to AWS lightsail.

### Summary of installed software

<b>
apt-get install --assume-yes linux-aws
apt-get install --assume-yes hibagent
apt-get upgrade
apt install finger
apt install git
apt install python
apt install apache2
apt-get install postgresql postgresql-contrib
apt install python-pip
apt install python3-pip
apt-get upgrade python3
apt-get install build-essential libssl-dev libffi-dev python-dev
apt install -y python3-venv
apt-get install libapache2-mod-wsgi
apt-get install python-psycopg2
apt-get install libpq-dev
apt install libapache2-mod-wsgi-py3
apt-get install ntp

</b>

### Python3 installation and configuration
Python3 installed using `apt install python3-pip`. Python3 virtual environment used for catalog application. 

`python3 -m venv catalogvenv`

Virtual environment is activated and application required python modules installed within virtual environment. <b>Virtual environment python-path</b> is used as python-path in the apache application configuration file. Also home path is set to where the application home is defined in /var/www/.

### Apache2 installation and configuration

Apache2 installed using the `apt install apache2` command. Additionally `apt install libapache2-mod-wsgi-py3` is used to install the wsgi to render the flask application using web server gateway interface. For Apache to render the Catalog Flask Application, following changes were made:

- directory is created for catalog app in /var/www folder
- a wsgi file created to invoke the flask application in /var/www folder
- git clone the application from github into ubuntu server /var/www application folder, below folder structure
<b>  
 .
 ..
 CatalogApp
 catalogapp.wsgi
.git
</b>
- python virtual env created to install the required python modules
- a new apache conf file created for this application specifying the home and python path in the WSGIDaemonProcess
  `CatalogApp python-path=/var/www/myapps/CatalogApp/CatalogApp/catalogvenv/lib/python3.5/site-packages home=/var/www/myapps/CatalogApp/CatalogApp`
- existing default site is dissited
- catalog site is ensited
- apache is restarted after the above changes
- `systemctl status apache2.service` is used to check the apache service status
- site is verified by passing the dns/ip address in the web browser
  

### Postgresql installation and configuration

Postgresql is installed using `apt-get install postgresql postgresql-contrib` command. A new database named <b> catalog</b> created in postgresql. Also, postgres user is associated with a password. Postgres runs in 5432 port. This info is used to generate the Database URI for python program to connect to catalog database.

`\connect catalog` is used to connect to catalog database. Also, a user named catalog is created with limited access to DB.

<b>
postgres=# \du
                                   List of roles
 Role name |                         Attributes                         | Member of
-----------+------------------------------------------------------------+-----------
 catalog   |                                                            | {}
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}

</b>

## Linux User Management
A new user named grader is created in the server, and can access th server only using ssh enabled port. `ssh-keygen` is used to generate the ssk key pair for grader, shared separately.

Verify the sshd_config file to confirm if password authentication is disabled by checking the property <b>PasswordAuthentication no</b>

### Requirements

- Amazon Lightsail
- Apache2
- Python3
- Postgresql
- pip install
  - flask
  - sqlalchemy
  - oauth2client.client
  - json
  - httplib2
  - random
  - venv

## Security
Server security is implemented using Ubuntu's Uncomlicated Firewall (ufw) tool. All packages are up to date and ssh port does not use the default 22 port. Also, UFW is configured for SSH (port 2200), HTTP (port 80), and NTP (port 123).

Application security is configured using google's oauth login and CRUD operations are enabled only whena  user is logged in. As well, current user can perform CRUD only on the catalogs owned by them. Additionally, applications' .git directory is disabled to access via web browser.

### The files for catalog application

Inside the linux server /var/www/myapps/CatalogApp/CatalogApp, run the  `ls -lrt`.

Following folders are in the catalog directory

- static
- templates
 
static folder contains the css and dependent files

templates folder contains the html files

<b> Under the catalog directory, below are important files for the application </b>

| S.No | File Name           | Usage                                          |
| ---- | ------------------- | ---------------------------------------------- |
| 1    | __init__.py         | This file runs the application                 |
| 2    | client_secrets.json | Holds the secret key for google authentication |
| 3    | database.ini        | Used for postgresql DB initialization          |
| 4    | database_init.py    | Creates the database with tables               |
| 5    | database_setup.py   | Initial data setup for the app                 |
| 6    | README.md           | Default readme file                            |
| 7    | requirements.txt    | pip install req file for easy pip install      |

### Accessing the application

Application can be access using [URL](mycatalogapp.net)

- [View Catalogs](http://mycatalogapp.net/catalog/)
- [Create new Catalogs](http://mycatalogapp.net/catalog/new/)
- [Edit a Catalog](http://mycatalogapp.net/catalog/6/edit/)
- [Delete a Catalog](http://mycatalogapp.net/catalog/6/delete/)
- [View Items in Catalog](http://mycatalogapp.net/catalog/6/)
- [Edit a Catalog Item](http://mycatalogapp.net/catalog/6/21/edit/)
- [Add a Catalog Item](http://mycatalogapp.net/book/new/6/)
- [Delete a Catalog Item](http://mycatalogapp.net/catalog/6/21/delete/)

<b>`Note` </b> CRUD operations will work only if the user has logged in to the application


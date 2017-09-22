# Payment Sandbox - Wordpress with WooCommerce Docker Compose

### A simple Wordpress Woocommerce Docker setup with 1 admin account, 1 customer account, 1 product and 1 product category using the Storefront theme.

#### Login Credentials

For the administration account: admin/password admin@example.com

For the Customer account: customer/password customer@example.com

## Setting up your environment

### Install Docker Toolbox with Kitematic

The easiest way to install and manage Docker containers is through Docker Toolbox with Kitematic.  Go to https://kitematic.com/ for an overview of the tool or to the download for Mac or Windows at https://www.docker.com/products/docker-toolbox

(Kitematic usage and additional installation help can be found here: https://docs.docker.com/kitematic/userguide/#working-with-a-container)

### Install GIT on your machine

If you do not have the GIT client locally installed on your machine you download and install here:  https://git-scm.com/book/en/v2/Getting-Started-Installing-Git

#### Download Payment Sandbox Woocommerce from GIT Repository

Open a terminal and create a directory where you will store the Code to launch Docker images of a MySQL database and a Wordpress image with Woocommerce plug-in.  In this directory, git clone the Payment Sandbox Docker-Compose setup.  This will download the code into a subfolder called paymentsanbox_wc which will also be your project name.  If you want to use another name for the subfolder/project just add your unique <name> after the download link

```
git clone https://github.com/rjpalermo1/paymentsandbox_wc.git
- or -
git clone https://github.com/rjpalermo1/paymentsandbox_wc.git myspecialname
```

Change directory into the new subfolder/project and/or open the project folder in your favorite code editor.

The project contains 3 folder and some root level files.
* **config** - provides a php file where you can set upload file size defaults
* **wp-app** - the location of the Payment Sandbox Wordpress/Woocommerce application that will link to the Wordpress docker image
* **wp-data** - the location of the Payment Sandbox Wordpress SQL data base that will link to the MySQL Docker image
* **docker-compose.yml** the Docker images main setup and configuration file
* **export.sh** a bash shell that when run will backup the current Docker image database to .bak file in the wp-data folder
* **README.md** this readme file

### Setting base image SQL data

In the **wp-data** folder is a data_mm_dd_yyyy.sql.bak file.  This file contains an sql script that includes the necessary schema, tables, fields and data to successfully launch the base Payment Sandbox Woocommerce instance.

COPY this file and save it as data_mm_dd_yyyy.sql (same name, just remove the .bak) in the **wp-data** folder.

Open the data_mm_dd_yyyy.sql file and make sure that line1 has two `--` in front of the statement `mysqldump: [Warning] Using a password on the command line interface can be insecure.`  Line 1 should look like this `--mysqldump: [Warning] Using a password on the command line interface can be insecure.`.  If you had to add the `--` make sure to save the .sql file.

>:  The export.sh automatically inserts this statement in the .bak file as a warning for insecure installations in a production environment.  If you make any modifications to the Payment Sandbox Woocommerce setup from the front-end tha you want to save 'as your own' custom setup, you will need to add the `--` to your .bak file after running the export.sh.  Detailed instructions on the export.sh are provided below.

## Launching Docker Servers

You can start the Docker server and create a Payment Sandbox Woocommerce instance with default settings on a fresh git download by going to your project root folder in a terminal and entering:

```
docker-compose up -d
```

This will run a series of commands to pull the latest Docker Wordpress and MySQL server images, assign localhost ports for Wordpress and MySql and link them in a virtual network, provide MySQL environment variables, link your local volumes (the wp-app and wp-data) to the server images and finally launch the sql script to populate the database with the latest Payment Sandbox Woocommerce base configuration.

The first time you run the docker-compose setup it will take several minutes as docker needs to fetch some pretty large 150MB (+/-) server images.  Subsequent docker-compose setups after the images have been installed locally takes only a few seconds.

Once the docker-compose process is complete, you will receive your prompt back in the terminal.  At this point, MySQL is loading the scripts while Wordpress waits.  When finished Wordpress will connect to the database and self-configure according to the configuration in the database.  This should only take 30 seconds to a minute.  You can open Kitematic and view the process by selecting either the MySql server image or the Wordpress image.

Once complete, you can open a browser at address localhost:2080 and you should at the Payment Sandbox Woocommerce Welcome page.

>NOTE:  If localhost returns a blank page to unable to connect page, clear yor browser history and cache, or open a private browser.

### Customizing the Docker Server setup

>NOTE:  Only recommended for experienced Docker and Woocommerce developers.  Recommed backing up docker-compose.yaml or the entire project prior to changing docker and server params to roll back if necessary.

You can modify the docker-compose.yaml to start the servers on different localhost ports and/or change the startup environment variables by modifying the docker-compose.yaml file.

You may set your host entry and change it in the database, or you simply overwrite it in the **wp-config.php** in the `wp-app/wp-includes/` folder by adding

```
define('WP_HOME','http://wp-app.local');
define('WP_SITEURL','http://wp-app.local');
```

The docker-compose.yaml file also has two 'volumes' (local directory links to the docker containers) that can be used for developing plugins and themes.

Finally, the docker-compose.yaml pulls the latest vendor-build images for Wordpress and MySQL.  If you require a Payment Sandbox Woocommerce setup with earlier versions of Wordpress or MySql you need to go to the vendor Docker image site on Docker Hub, find the 'Tag' that represents the version you need and replace the `image:` in the docker-compose.yml accordingly.

For example:

```
  wordpress:
    image: wordpress:4-php5.6-apache

  db:
    image: mysql:5.7.19

```

Wordpress docker images can be found: https://hub.docker.com/_/wordpress/

MySQL docker images can be found:  https://hub.docker.com/_/mysql/


## Docker Commands

Kitematic is a useful tool for stopping, restarting, deleteing and even getting to the code running in the containers (the server 'virtual boxes' for Wordpress and MySql) through the `exec` button when the image is selected.  You can also manage your entire docker setup through the terminal command line.  The following cheat sheet provdes useful commands for managing your Docker environment from the command-line.

https://www.digitalocean.com/community/tutorials/how-to-remove-docker-images-containers-and-volumes

## Creating your own custom Payment Sandbox Woocommerce setup

After you launch the base setup yu'll have a complete Docker development environment with Wordpress/Woocommerce backed by a MySQL database.  You can add your own themes, plugins, products and customers then save the custom setup as your own.

The simplest way to do this is to leave your custom project in the original folder you created for the Payment Sandbox base setup.  The SQL database with the current configuration, the Wordpress/Woocommerce core files and any new plugins and themes you install are already linked in the **wp-admin** and **wp-data** folders.  If you need to start a fresh install of the Payment Sandbox base servers just create a new folder, clone from the Payment Sandbox git repository and follow the directions above.

## Backing up your custom configuration database

When you are satisfied with your new Woocommerce setup you can go to your project root folder and ener the following command from terminal:

```
. export.sh
```

This will generate a sql script from the current database with all your settings and create a .bak file in **wp-data** named dd_mm_yyyy.sql.bak.  Now your project is portable and backed up and immune to losing the custom setup if docker crashes or you unintentionally remove the Docker containers that were linked to the local **wp-data** and **wp-app** folders.

>NOTE:  If you have insecure passwords and have to select the option 'Confirm insecure password' when creating users in the Wordpress interface, the sql backup script will create a line 1 that says `mysqldump: [Warning] Using a password on the command line interface can be insecure.`.  Make sure that if you ever move this custom setup or need to rebuild your containers and copy the `dd_mm_yyyy.sql.bak` as `dd_mm_yyyy.sql` linked to your new MySql container that line has has two `--` preceding it to look like `-- mysqldump: [Warning] Using a password on the command line interface can be insecure.` or you will get an error.

## Creating your own git repository

When you `git clone` from the payment sandbox it links the code in your project base folder to the .git repository you cloned from.  If you are going to keep a custom setup you probably should remove the hidden .git folder in the root of the project and setup your own git repository to track your changes during the life of your project.




---

## Developing a Theme

Configure the volume to load the theme in the container in the docker-compose.yml.  For each theme, add an entry to the volumes: in the docker-compose.yml that will link your local project/<theme-name> to the Wordpress docker container serving the Woocommerce instance.  Replace the <theme-name> in the following statement with your theme name.

```
volumes:
  - ./<theme-name>/trunk/:/var/www/html/wp-content/themes/<theme-name>
```

This will create a folder in your local project

## Developing a Plugin

Configure the volume to load the plugin in the container in the docker-compose.yml.  For each plugin, add an entry to the `volumes:` in the docker-compose.yml that will link your local project/<plugin-name> to the Wordpress docker container serving the Woocommerce instance.  Replace the <plugin-name> in the following statement with your plugin name.

```
volumes:
  - ./<plugin-name>/trunk/:/var/www/html/wp-content/plugins/<plugin-name>
```
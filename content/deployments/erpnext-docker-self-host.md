# How to Install ERPNext on a Docker
ERPNext is a powerful and flexible software that lets you run your business smoothly. It covers all the essential aspects of your business, such as accounting, inventory, sales, purchasing, human resources, projects, and more. And the best part is, it's free and open source. That means you can use it without paying a dime, and you can also peek under the hood and see how it works. Or even better, you can contribute to its development and make it even more awesome. You can find the source code of ERPNext on [GitHub][1], where you can also report issues, suggest features, or join the community of developers and users.

In this blog post, I will guide you through the steps to install ERPNext on a docker instance using a ready-made image. This image is called python:3.11-slim and it comes with Debian Buster and Python 3.11 pre-installed. All you need is some basic knowledge of docker and a few commands to get started. If you don't know what docker is, don't worry, it's not a scary monster that lives in the sea. It's a tool that lets you create and run isolated containers that contain everything you need to run an application. Think of it as a magic box that you can put your software in and take it anywhere you want. You can learn more about docker [here][2].

Before we jump into the nitty gritty, let me tell you why I chose this image for ERPNext. First of all, it's based on Debian Buster, which is a stable and secure Linux distribution that powers many servers around the world. Second, it has Python 3.11 installed, which is the latest version of the programming language that ERPNext is written in. Python 3.11 has many new features and improvements that make ERPNext faster and better. Third, it's slim, which means it has only the essential packages and libraries that ERPNext needs to run. This makes the image smaller and lighter than other images that have extra stuff that ERPNext doesn't use. You can find the image on Docker Hub [here][3].

Now that you know what we are going to use, let's get started with the installation process.

[1]: https://github.com/frappe/erpnext
[2]: https://docs.docker.com/get-started/overview/
[3]: https://hub.docker.com/_/python

## Prerequisites
Before we start, make sure you have the following:
 - A docker instance running on your machine or cloud provider
 - A terminal or SSH access to the docker instance
 - A sudo user with root privileges on the docker instance

## Step 1: Update the System Packages
The first step is to update the system packages on the docker instance. This will ensure that we have the latest security patches and bug fixes. To do this, run the following command:

```bash
# Update the system packages
sudo apt update && sudo apt upgrade -y
```
This command will fetch the list of available packages from the repositories and upgrade any outdated packages.
## Step 2: Install Some Dependencies for ERPNext
The next step is to install some dependencies for ERPNext. These are:
 - python3-dev: This package contains the header files and libraries needed to build Python extensions.
 - python3-setuptools: This package contains tools for packaging and distributing Python projects.
 - python3-pip: This package contains the Python package manager that allows us to install Python modules from PyPI.
 - mariadb-server: This package contains the MariaDB database server that will store the data for ERPNext.
 - redis-server: This package contains the Redis server that will act as a cache and message broker for ERPNext.
 - nginx: This package contains the Nginx web server that will serve the static files and proxy requests for ERPNext.
 - wkhtmltopdf: This package contains a tool that converts HTML to PDF, which is used by ERPNext to generate reports and invoices.
 - curl: This package contains a tool that transfers data from or to a server using various protocols.

To install these dependencies, run the following command:

```bash
# Install some dependencies for ERPNext
sudo apt install -y curl sudo cron python3-dev python3-setuptools python3-pip mariadb-server redis-server nginx wkhtmltopdf git
```
This command will install all the required packages and their dependencies.
## Step 3: Configure MariaDB Server for ERPNext Version 14
The next step is to configure the MariaDB server for ERPNext version 14. This version of ERPNext requires a specific collation setting for the database server, which is utf8mb4_unicode_ci. This setting ensures that the database can store and compare characters from different languages correctly.
To configure this setting, we need to edit the file /etc/mysql/mariadb.conf.d/50-server.cnf and change the value of collation-server to utf8mb4_unicode_ci. To do this, run the following command:

```bash
#Configure mysql server for erpnext version 14
sudo sed -i 's/^collation-server\s*=.*/collation-server = utf8mb4_unicode_ci/' /etc/mysql/mariadb.conf.d/50-server.cnf
```
This command will use sed, a stream editor, to find and replace the collation-server line in the file with the desired value.
## Step 4: Initialize and Set the Password for MariaDB Server
The next step is to initialize and set the password for the MariaDB server. By default, the MariaDB server does not have a password for the root user, which is insecure. We need to set a password for the root user and use it later when creating a database for ERPNext.
To initialize and set the password for the MariaDB server, run the following commands:

```bash
#Now initialize and set the password
service mysql start
mysql -e "ALTER USER 'root'@'localhost' IDENTIFIED BY 'erpnext'"
```
The first command will start the MariaDB service. The second command will use mysql, a command-line client, to execute an SQL statement that changes the password of the root user to erpnext. You can change this password to something more secure if you want.
## Step 5: Create a User for ERPNext and Switch to It
The next step is to create a user for ERPNext and switch to it. We will use this user to run ERPNext and avoid using root or sudo privileges unnecessarily. We will also add this user to the sudo group so that we can execute some commands with root privileges when needed.
To create a user for ERPNext and switch to it, run the following commands:

```bash
# Create a user for ERPNext and switch to it
sudo adduser erpnext --disabled-password --gecos ""
sudo usermod -aG sudo erpnext
sudo su - erpnext
```
The first command will create a user named erpnext with no password and no personal information. The second command will add the user to the sudo group. The third command will switch to the user's shell.
## Step 6: Install NVM and Node
The next step is to install NVM and Node. NVM is a tool that allows us to manage multiple versions of Node on the same machine. Node is a JavaScript runtime that is used by ERPNext to run some scripts and tools.
To install NVM and Node, run the following commands:

```bash
# install nvm and node
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
exit
su - erpnext
```
The first command will download and execute the install script for NVM from its GitHub repository. The second command will exit the current shell and return to the root user. The third command will switch back to the erpnext user's shell.
## Step 7: Clone the ERPNext Repository from GitHub
The next step is to clone the ERPNext repository from GitHub. This repository contains the source code and files for ERPNext. We will use the --depth 1 option to clone only the latest commit and save some disk space.
To clone the ERPNext repository from GitHub, run the following command:

```bash
# Clone the ERPNext repository from GitHub
git clone https://github.com/frappe/erpnext.git --depth 1
```
This command will use git, a version control system, to clone the repository from its URL.
## Step 8: Install the frappe-bench Tool Using pip
The next step is to install the frappe-bench tool using pip. This tool is a command-line interface that helps us manage multiple sites and apps for ERPNext. It also handles the installation, configuration, backup, and update of ERPNext.
To install the frappe-bench tool using pip, run the following command:

```bash
# Install the frappe-bench tool using pip
pip3 install frappe-bench
```
This command will use pip3, a Python package manager, to install frappe-bench from PyPI.
## Step 9: Initialize a New Bench with ERPNext
The next step is to initialize a new bench with ERPNext. A bench is a folder that contains one or more sites and apps for ERPNext. We will use the --frappe-branch version-13 option to specify that we want to use version 13 of ERPNext, which is the latest stable release as of this writing. We will also use the --python python3 option to specify that we want to use Python 3 as the interpreter.
To initialize a new bench with ERPNext, run the following command:

```bash
# Initialize a new bench with ERPNext
bench init erpnext-bench --frappe-branch version-13 --python python3
```
This command will use bench, a command-line interface, to create a new folder named erpnext-bench and install frappe, an app framework that powers ERPNext, in it.
## Step 10: Change the Directory to the Bench Folder
The next step is to change the directory to the bench folder. This folder contains all the files and folders related to our ERPNext installation. We need to be in this folder to execute some commands later.
To change the directory to the bench folder, run the following command:

```bash
# Change the directory to the bench folder
cd erpnext-bench
```
This command will use cd, a shell builtin, to change the current working directory.
## Step 11: Add ERPNext as an App to the Bench
The next step is to add ERPNext as an app to the bench. An app is a collection of modules, doctypes, pages, reports, and scripts that provide some functionality for ERPNext. We will use the --branch version-13 option to specify that we want to use version 13 of ERPNext, which is compatible with frappe version 13.
To add ERPNext as an app to the bench, run the following command:

```bash
# Add ERPNext as an app to the bench
bench get-app erpnext https://github.com/frappe/erpnext.git --branch version-13
```
This command will use bench, a command-line interface, to download and install ERPNext from its GitHub repository in our bench folder.
## Step 12: Install the ERPNext App on a New Site
The next step is to install the ERPNext app on a new site. A site is a folder that contains the database and files for a single instance of ERPNext. We can have multiple sites on the same bench and switch between them. We will use the --mariadb-root-password erpnext option to specify the password for the MariaDB root user that we set earlier. We will also use the --admin-password admin123 option to specify the password for the administrator user of ERPNext. You can change these passwords to something more secure if you want.
To install the ERPNext app on a new site, run the following command:

```bash
# Install the ERPNext app on a new site
bench new-site erpnext.local --mariadb-root-password erpnext --admin-password admin123
```
This command will use bench, a command-line interface, to create a new folder named erpnext.local and initialize a database and files for it. It will also install frappe and ERPNext on the site.
## Step 13: Install ERPNext on the Site
The next step is to install ERPNext on the site. This step will set up some default data and configurations for ERPNext, such as the company name, currency, timezone, email settings, etc. We will use the --site erpnext.local option to specify the name of the site that we want to install ERPNext on.
To install ERPNext on the site, run the following command:

```bash
# Install ERPNext on the site
bench --site erpnext.local install-app erpnext
```
This command will use bench, a command-line interface, to install ERPNext on the site erpnext.local.
## Step 14: Start the Bench Server
The final step is to start the bench server. This step will start the processes that run ERPNext, such as the web server, the background workers, and the scheduler. We will use the bench start command to start the server.
To start the bench server, run the following command:

```bash
# Start the bench server
bench start
```
This command will use bench, a command-line interface, to start the server and display some logs and messages.
## Conclusion
We have successfully installed ERPNext on a docker instance from image python:3.11-slim using some commands. We can now access our ERPNext site by visiting http://erpnext.local:8000 in our browser. We can log in with the username Administrator and the password admin123 that we set earlier.
I hope you found this blog post helpful and informative. If you have any questions or feedback, please leave a comment below. Thank you for reading! 😊

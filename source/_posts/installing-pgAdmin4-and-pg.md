---
title: installing pgAdmin4 and pg on Ubuntu
date: 2019-05-16 14:30:05
tags: pgAdmin4 postgresql
categories: postgresql
thumbnail: /gallery/pgAdmin4-1.png
---


## PgAdmin 4 Installation Steps
1. Update the system       
Before starting the installation process, you have to update the system by executing the following command. 

```bash
$sudo apt update   
```    

![pgadmin](/pgadmin.png)

 
<!--more-->

2. Install required packages       
Three packages require to install before downloading pgAdmin which are python, pip and virtualenv. Run the following command to install these packages.      

```bash
$ sudo apt install build-essential libssl-dev libffi-dev libgmp3-dev virtualenv 
```        

![pgadmin2](/pgadmin2.png) 

When the following prompt appears then press ‘y’ to complete the installation process of the required packages.       

![pgadmin-1](/pgadmin-1.png)  

3. Create virtual environment       
Run the following commands to create a new folder named pgAdmin4 in the current location, go to the newly created folder and create the virtual environment.        

```bash
$ mkdir pgAdmin4
$ cd pgAdmin4
$ virtualenv pgAdmin4   
```     

4. Activate virtual environment          
Go to pgAdmin4 folder under pgAdmin4 and run the following commands to activate the virtual environment.        

```bash
$ cd pgAdmin4      
$ source bin/activate      
```   

5. Download pgAdmin 4     
Run the following command to download the latest version of pgAdmin 4.
pgadmin4-4.6-py2.py3-none-any.whl      

```bash
$ wget https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -pgadmin4-4.6-py2.py3-none-any.whl
```   

After completing the download of pgAdmin, the console will be look like the following image.      

6. Install pgAdmin 4        
Run the following command to complete the installation process of pgAdmin 4.   

```bash     
$ pip install pgadmin4-4.6-py2.py3-none-any.whl      
```   

7. Configure and run pgAdmin 4           
After completing the installation steps, you have to create a configuration file to run this software. Create a new file named config_local.py in lib/python2.7/site-packages/pgadmin4/ folder using nano editor.   

```bash       
$ nano lib/python2.7/site-packages/pgadmin4/config_local.py
Add the following content in config_local.py.

import os
DATA_DIR = os.path.realpath(os.path.expanduser(u'~/.pgadmin/'))
LOG_FILE = os.path.join(DATA_DIR, 'pgadmin4.log')
SQLITE_PATH = os.path.join(DATA_DIR, 'pgadmin4.db')
SESSION_DB_PATH = os.path.join(DATA_DIR, 'sessions')
STORAGE_DIR = os.path.join(DATA_DIR, 'storage')
SERVER_MODE = False
```   

Now, use the following command to run pgAdmin.         
```bash   
$ python lib/python2.7/site-packages/pgadmin4/pgAdmin4.py
```     
***Note: If any flask-htmlmin module error appears then run the following commands to install the module and then run the server.  

```bash    
$ pip install flask-htmlmin
$ python lib/python2.7/site-packages/pgadmin4/pgAdmin4.py
```   
The following message will appear if pgAdmin can run successfully.    

##  Using pgAdmin 4      
Before using pgAdmin, you need to confirm that the latest version of PostgreSQL is installed on your operating system. Run the following command to install postgres package for PostgreSQL and contrib package for taking additional functionalities related to PostgreSQL.        

```bash  
$ sudo apt install postgresql postgresql-contrib  
```  

Next, you have to create the username and password for PostgreSQL database. Type the following command in the terminal to add login credentials for the user, postgres.   
```bash  
$ sudo -u postgres psql postgres   
```    

Setup the password for the username, ‘postgres’. It will ask to type the password two times for confirmation.      
 \password postgres       
 If you want to quit from the interactive session of postgres then type ‘\q’.       
 PostgreSQL is now ready to use. Go to the pgAdmin browser and click on the ‘Add New Server’ option.     

## Running easier pgAdmin4    
Steps to execute a shell script in Linux      

The procedure is as follows:       

1. Create a new file called demo.sh using a text editor such as nano or vi in Linux:
      nano demo.sh
2. Add the following code:

```bash      
#!/bin/bash
cd ~/pgadmin4
source bin/activate
python lib/python2.7/site-packages/pgadmin4/pgAdmin4.py
```      

3. Set the script executable permission by running chmod command in Linux: 
chmod +x demo.sh       
4. Execute a shell script in Linux:    
 ./demo.sh       















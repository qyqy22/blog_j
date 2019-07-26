---
title: addPostgis
date: 2019-06-21 10:05:45
author: Jacqueline.zhang
tags:
categories:
thumbnail: /gallery/cat.jpg
toc: true
---
### create a complex extension
For example : create a postgis   
if you install postgis at first, there are some information as viewed below:
Geos-config not found
gdal-config not found
Sudo apt install libgdal-dev 
You should install proj and geos first

<!--more-->
#### install proj
```bash     
tar -xvzf proj-4.8.0.tar.gz
cd proj-4.8.0
./configure --prefix=/usr/local/proj
make
make install    
```         
Some information ask you to install segments.
'install-recursive' failed
you have to install recursive
#### install geos 
```bash       
tar -jxvf geos-3.3.3.tar.bz2
cd geos-3.3.3
./configure --prefix=/usr/local/geos
make
make install
```       

install swig
Install python-dev  

```bash   
$ ./configure --prefix=/home/postgres/Downloads/geos-3.7.2/build --enable-python 
$ /Downloads/geos-3.7.2/build/bin
$ geos-config --version
> 3.7.2
```     

set Enviorment    

```bash   
export LD_LIBRARY_PATH="/opt/geos-3.3.3/build/lib:$LD_LIBRARY_PATH"
export PATH="/opt/geos-3.3.3/build/bin:$PATH"
```

#### install postgis
```bash   
tar -xvzf postgis-1.5.4.tar.gz
cd postgis-1.5.4
./configure --with-pgconfig=/usr/local/pgsql/bin/pg_config --with-projdir=/usr/local/proj --with-geosconfig=/usr/local/geos/bin/geos-config
```      

pg_ctl -D /data/pgsql/ -l /data/pgsql/pgsql.log start     
createdb template_postgis       
psql -f /usr/local/pgsql/share/contrib/postgis-1.5/postgis.sql -d template_postgis       
psql template_postgis     
select postgis_full_version();       

![selectpostgis](/selectpostgis.png)


reference
http://www.voidcn.com/article/p-yesruvml-bdv.html
---
title: How To Add Extensions To PG on Ubuntu
date: 2019-06-20 10:32:00
author: Jacqueline.zhang
tags: postgresql 
categories: extension
thumbnail: /gallery/news1.jpg
toc: true
---
## How Extensibility Works
PostgreSQL is extensible because its operation is catalog-driven.

PostgreSQL stores much more information in its catalogs: not only information about tables and columns, but also information about data types, functions, access methods, and so on. These tables can be modified by the user, and since PostgreSQL bases its operation on these tables, this means that PostgreSQL can be extended by users.

The PostgreSQL server can moreover incorporate user-written code into itself through dynamic loading.
<!-- more -->

### The 3 directory for Extension directory
The following three directories are used for storing the files related to creating a extension in PG, these are :

1- /path/to/source/tree/contrib/ - This is top level directory for extension in the PostgreSQL source code.

2- /path/to/source/tree/build_dir/contrib - If you build original code in build_dir directory, the contrib code will be built in the same build_dir path.
If you build original code in the source tree, the contrib code will be built in the source tree dir not in the build_dir directory. The build_dir depends whether you mkdir it.

3- /path/to/source/tree/psql/share/extension - I install all files under the directory `/path/to/source/tree/psql` instead of `/usr/local/pgsql`. So the extension is intall under the path. This is the shared library for the extension is stored.When I install my own extensions, the extension will be stored under /share/extension path.


### There are two kinds of ways to add extension.
The PostgreSQL server comes pre-installed with a number of extensions, in order to load these extensions in the database the user needs to run the "create extension" command and provide a name of the extension. The corresponding extension files (mentioned below) are excuted in order to load the extension in the database.

PostgreSQL also allows the user to create there own extensions and load them in the database. This blog focusses on `how you can create your own extensions` to PostgreSQL and load them in the database.

## How to Use PostgreSQL Community Supported Extensions  
run the "create extension" command and provide a name of the extension.
>CREATE EXTENSION [ IF NOT EXISTS ] extension_name
>    [ WITH ] [ SCHEMA schema_name ]
>             [ VERSION version ]
>             [ FROM old_version ]
>             [ CASCADE ]

for example I create extension pg_buffercache.

```sql
postgres=# create extension pg_buffercache; 
postgres=# \dx
List of installed extensions
Name | Version | Schema | Description
--------------------+---------+------------+-------------------------------------------------
pg_buffercache | 1.3 | public | examine the shared buffer cache
pg_stat_statements | 1.7 | public | track execution statistics of all SQL statements executed
plpgsql | 1.0 | pg_catalog | PL/pgSQL procedural language      
```




## Add a your own extension on PG    
To be able to run the create extension command in your database,your extension needs at least two files: a control file and a extension's SQL script file in the format.      
The simple plpgsql function allows us to encode any integer into its base36 representation. If we copied these two files into postgres SHAREDIR/extension directory, then we could start using the extension with CREATE EXTENSION. But we won’t bother users with figuring out where to put these files and how to copy them manually – that’s what Makefiles are made for. So, let’s add one to our project.  
 
### Create base36 extension to pg

create a base36 folder 
I add base36 folder under the directory (/path/to/source/tree/build_dir/contrib/)
> $ mkdir base36  --you can put anywhere you like.   

add base36--0.0.1.sql, base36. control, Makefile  in the folder

#### Control file   

base36.control   
  
>comment = 'base36 datatype'
>default_version = '0.0.1'
>relocatable = true
            

#### SQL file

base36--0.0.1.sql   

```sql    
-- complain if script is sourced in psql, rather than via CREATE EXTENSION
\echo Use "CREATE EXTENSION base36" to load this file. \quit
CREATE FUNCTION base36_encode(digits int)
RETURNS text
LANGUAGE plpgsql IMMUTABLE STRICT
  AS $$
    DECLARE
      chars char[];
      ret varchar;
      val int;
    BEGIN
      IF digits = 0
        THEN RETURN('0');
      END IF;
      chars := ARRAY[
                '0','1','2','3','4','5','6','7','8','9','a','b','c','d','e','f','g','h',
                'i','j','k','l','m','n','o','p','q','r','s','t', 'u','v','w','x','y','z'
              ];

      val := digits;
      ret := '';

    WHILE val != 0 LOOP
      ret := chars[(val % 36)+1] || ret;
      val := val / 36;
    END LOOP;

    RETURN(ret);
    END;
  $$;         
```




Most of the environment variables needed to build an extension are setup in pg_config and can simply be reused.

> export PGDATA=/home/postgres/postgresql/pg11/psql/data(data directory)
> export PATH=/home/postgres/postgresql/pg11/psql/bin(postgresql bin directory)

#### Makefile
Every PostgreSQL installation from 9.1 onwards provides a build infrastructure for extensions called PGXS, allowing extensions to be easily built against an already-installed server. Most of the environment variables needed to build an extension are setup in pg_config and can simply be reused.

For our example this Makefile fits our needs.

Makefile

```bash      
EXTENSION = base36        # the extensions name
DATA = base36--0.0.1.sql  # script files to install

# postgres build stuff
PG_CONFIG = pg_config
PGXS := $(shell $(PG_CONFIG) --pgxs)
include $(PGXS)
```



Now we can start using the extension run

     
> $ make install

.sql and .control will be installed to dir $PGDATA/share/extension/  
You can see the extension was installed in the share/extension path.

![makeinstalladdexten](/makeinstalladdexten.png)

#### Regress test
I also add a regress test in Makefile and add regress test SQL as viewed below.

![regresstest](/regresstest.png)

Add `REGRESS = base36_test`   in the Makefile as viewed below:    

```bash      
EXTENSION = base36        # the extensions name
DATA = base36--0.0.1.sql  # script files to install
REGRESS = base36_test     # our test script file (without extension)
# postgres build stuff
PG_CONFIG = pg_config
PGXS := $(shell $(PG_CONFIG) --pgxs)
include $(PGXS)
```



add regress test sql   
```bash
$ mkdir sql
$ mkdir result
```



add sql/base36_test.sql  

```sql
CREATE EXTENSION base36;
SELECT base36_encode(0);
SELECT base36_encode(1);
SELECT base36_encode(36);
SELECT base36_encode(123456789);
```



Regress test begin 

```bash    
$ make installcheck
$ mkdir expected 
$ mv results/base36_test.out to expected
$ make installcheck 
```




regress test result    
![makeinstallcheck](/makeinstallcheck.png)

create extension base36      
![createbase36](/createbase36.png)

####  C- Language function to make it work with postgres

add base36.c and update base36-0.0.1 to base36-0.0.2.sql, and update Makefile and base36.control as viewed below    
![addc](/addc.png) 

> drop extension base36;

base36–0.0.2.sql 

```sql
\echo Use "CREATE EXTENSION base36" to load this file. \quit
CREATE FUNCTION base36_encode(integer) RETURNS text
AS '$libdir/base36'
LANGUAGE C IMMUTABLE STRICT;  # this part for C language 
```


     
base36.control  

```bash
# base36 extension
comment = 'base36 datatype'
default_version = '0.0.2'
relocatable = true
```



Makefile   

```bash
EXTENSION = base36        # the extensions name
DATA = base36--0.0.1.sql  # script files to install
REGRESS = base36_test     # our test script file (without extension)
MODULES = base36          # our c module file to build

# postgres build stuff    
PG_CONFIG = pg_config
PGXS := $(shell $(PG_CONFIG) --pgxs)
include $(PGXS)
```
     
base36.c    
```c
#include "postgres.h"
#include "fmgr.h"
#include "utils/builtins.h"

PG_MODULE_MAGIC;

PG_FUNCTION_INFO_V1(base36_encode);
Datum
base36_encode(PG_FUNCTION_ARGS)
{
    int32 arg = PG_GETARG_INT32(0);
    if (arg < 0)
        ereport(ERROR,
            (
             errcode(ERRCODE_NUMERIC_VALUE_OUT_OF_RANGE),
             errmsg("negative values are not allowed"),
             errdetail("value %d is negative", arg),
             errhint("make it positive")
            )
        );
    char base36[36] = "0123456789abcdefghijklmnopqrstuvwxyz";

    /* max 6 char + '\0' */
    char *buffer = palloc(7 * sizeof(char));
    unsigned int offset = sizeof(buffer);
    buffer[--offset] = '\0';

    do {
        buffer[--offset] = base36[arg % 36];
    } while (arg /= 36);


    PG_RETURN_TEXT_P(cstring_to_text(&buffer[offset]));
}
```

Then we command again. `$ make install & make installcheck`
> create extension base36;
       

You will see as below
![addbase02](/addbase02.png) 

I add function in base36.c

```c      
if (arg < 0)
    ereport(ERROR,
        (
         errcode(ERRCODE_NUMERIC_VALUE_OUT_OF_RANGE),
         errmsg("negative values are not allowed"),
         errdetail("value %d is negative", arg),
         errhint("make it positive")
        )
    );

```       

result
![cfunction](/cfunction.png) 



## reference
https://www.postgresql.org/docs/12/extend.html
https://www.postgresql.org/docs/12/contrib.html
http://big-elephants.com/2015-10/writing-postgres-extensions-part-i/



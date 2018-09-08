### 15.0 Install MySQL

I am not using Chocolatey for this install.  Instead, download and run the MySQL MySQL Community Edition 8.0.x Windows [installer](https://dev.mysql.com/downloads/windows/installer/8.0.html).

__INFO__: choose the "mysql-installer-web-community-8.0.12.0.msi" file (for online connections).

You will be prompted to sign in or create an Oracle account prior to performing this operation.  Once authenticated, click the fat blue "Download Now" button to initiate the install process.  Select "Run" when Windows asks what to do with the *.msi file and then select "Yes" when prompted to allow the installer to make changes to your device.

#### License Agreement Screen / Choosing a Setup Type Screen
The installer will search for previously installed packages.  Assuming previous installs are not found, agree to the license terms and then choose your setup type.  I chose "Custom".  Then click the "Next" button.

#### Select Products and Features Screen
The left-side box of the "Select Products and Features" screen lists the available products:

```
+ MySQL Servers
+ Applications
+ MySQL Connectors
+ Documentation
```

Click the "+" sign in front of "MySQL Servers" and select the appropriate MySQL Server for your machine.  Then click the green right arrow button to move the selection to the right-hand box.  Repeat the selection process for the following products, clicking through until you reach each of the featured product installs and then moving them to the right-hand box:

MySQL Workbench 8.x
MySQL Shell 8.0.x
MySQL Router 8.x
MySQL Documents 8.x
MySQL Notifier 1.1.x
MySQL Utilities

After completing your selection of MySQL products to install click the "Next" button.

#### Check Requirements Screen
You may encounter a "Check Requirements" screen.  Depending on previous Window product installs you may need to install additional software (e.g., Microsoft Visual C++ 2015 Redistributable).  Select the required software and click the "Execute" button.  Once all the required software is installed click the "Next" button.

#### Installation Screen
Click the "Execute" button to initiate installation of the MySQL products you have selected.  Once all products are installed click the "Next" button.

#### Product Configuration
Use the configuration wizard to adjust product settings.  Click the "Next" button to begin.

* Group Replication Screen -- select "Standalone MySQL Server /Classic MySQL Replication" then click the "Next" button.
* Type and Networking Screen
  - Server Configuration Type -- select "Development Computer".
  - Connectivity -- accept defaults (TCP/IP; Port Number: 3306 (unless already in use), etc.).
  - Advanced Configuration -- ignore and click the "Next" button.
* Authentication -- select "Use Strong Password Encryption for Authentication" then click the "Next" button.
* Accounts and Roles -- provide a password for the "Root" user.  __WARNING__: write this password down and store it in a safe place.  You will need it later.  You can add other users later.
* Windows Service
  - __WARNING__: click the check box for "Configure MySQL as a Windows Service"
  - Windows Service Name -- "MySQL80"
  - __WARNING__: click the check box for "Start the MySQL Server at System Startup".
  - Run Windows Service as ... -- select "Standard System Account".  Click the "Next" button.

#### Plugins and Extensions Screen
Ignore and click the "Next" button.

#### Apply Configuration Screen
Click the "Execute" button to apply the configuration changes. Upon completion click the "Next" button.

#### Installation Complete Screen
Click the "Finish" button.


## NEXT START SHELL AND CONFIRM
To confirm that all is well with the install log into the shell as root and issue the `SHOW DATABASES` command.  Then exit the shell.

```
C:\Users\arwhyte>mysql --user=root --password
Enter password: *********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 20
Server version: 8.0.12 MySQL Community Server - GPL

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
6 rows in set (0.00 sec)

mysql> exit
Bye
```

#### Install the Python mysqlclient Connector

The Django Team [recommends](https://docs.djangoproject.com/en/2.1/ref/databases/#mysql-notes) using the [mysqlclient](https://pypi.python.org/pypi/mysqlclient) to connect to MySQL.  You can install it using `pip`.

```
(venv) PS C:\Users\arwhyte\Development\repos\github\arwhyte\django-tutorial-mysql> pip install mysqlclient
```

__WARNING__: more than likely the install attempt will fail with an ugly error message that I've slimmed down to the following line:

```
...
 _mysql.c(29): fatal error C1083: Cannot open include file: 'mysql.h': No such file or directory
    error: command 'C:\\Program Files (x86)\\Microsoft Visual Studio\\2017\\BuildTools\\VC\\Tools\\MSVC\\14.15.26726\\bin\\HostX86\\x64\\cl.exe' failed with exit status 2
...
```

I was able to resolve this roadblock by resorting to Christoph Gohlke's collection of [Unoffical Windows Binaries for Python Extension Packages](https://www.lfd.uci.edu/~gohlke/pythonlibs/).

Download the appropriate the [mysqlclient](https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysqlclient) the wheel (*.whl) file.  For Python 3.7 click on "mysqlclient‑1.3.13‑cp37‑cp37m‑win_amd64.whl" and it will download to your machine.  Then perform a manual install via `pip`:

```
(venv) PS C:\Users\arwhyte\Development\repos\github\arwhyte\django-tutorial-mysql> pip install C:\Users\arwhyte\Download
s\mysqlclient-1.3.13-cp37-cp37m-win_amd64.whl
Processing c:\users\arwhyte\downloads\mysqlclient-1.3.13-cp37-cp37m-win_amd64.whl
Installing collected packages: mysqlclient
Successfully installed mysqlclient-1.3.13
```

### Create MySQL Users




### Passwords




### macOS: add a MySQL my.cnf Options File

Go to /etc/mysql and add/edit the my.cnf options file.  The example below


```
# ------------------------
# MYSQL CLIENT APP OPTIONS
# ------------------------
[client]

# MySQL Server will listen on this TCP/IP port.
port=3306

# MySQL Server will utilize this pipe.
socket=/tmp/mysql.sock

# The default non-root user.
user=arwhyte

# If you use LOAD DATA LOCAL INFILE in scripts that read the [client] group from option
# files, you can add an local-infile option setting to that group. To prevent problems
# for programs that do not understand this option, specify it using the loose- prefix.
# See https://dev.mysql.com/doc/refman/8.0/en/load-data-local.html
# Values
# 0 disabled
# 1 enabled
loose-local-infile=1

# --------------------
# MYSQL SERVER OPTIONS
# --------------------
[mysqld]

# MySQL Server will listen on this TCP/IP port.
port=3306

# MySQL Server will utilize this pipe.
socket=/tmp/mysql.sock

# The Key Buffer is used to cache index blocks for MyISAM tables. Do not set it larger
# than 30% of your available memory, as some memory is also required by the OS to cache
# rows. Even if you're not using MyISAM tables, you should still set it to 8-64M as it
# will also be used for internal temporary disk tables.
key_buffer_size=16M

# Enable local-infile if LOAD DATA INFILE statements are scoped with the LOCAL keyword.
# See https://dev.mysql.com/doc/refman/8.0/en/load-data.html
# See https://stackoverflow.com/questions/36023339/mysql-python-load-data-local-infile-error
# Values
# 0 disabled
# 1 enabled
local-infile=1

# Specifies how database, table and table alias names are stored in the metadata.
# Values
# 0 will throw an error on case-insensitive operative systems
# 1 table names are stored in lowercase on disk and comparisons are not case sensitive.
# 2 table names are stored as given but compared in lowercase.
#lower_case_table_names=1

# Max size of 1 packet or any generated or intermediate string, or any parameter
# sent by the mysql_stmt_send_long_data() C API function.
max_allowed_packet=128M

# Limits which directories you can load files from when using LOAD DATA INFILE.
# See https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_secure_file_priv
# Values
# ''       disabled. No restrictions placed on file locations.  This is NOT a secure setting.
# dir_name MySQL Server limits import/export operations to work only with files in the named
#          directory. The directory must exist; the server will not create it.
# Null     import/output operations are not permitted.
secure-file-priv=''

# -------------------------
# MYSQLDUMP UTILITY OPTIONS
# See https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html
# -------------------------
[mysqldump]

# quick option dumps tables row by row.
quick
```

### Update Django settings.py
Update the Django `settings.py` databases configuration setting as follows.


#### macOS/*nix
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'polls',
        'USER': 'django',
        'OPTIONS': {
            'read_default_file': '/etc/mysql/my.cnf',
        },
    }
}
```

#### Windows
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'polls',
        'USER': 'django',
        'OPTIONS': {
            'read_default_file': 'C:\ProgramData\MySQL\MySQL Server 8.0\my.ini',
        }
    }
}
```
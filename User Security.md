## Create and manage Database user accounts

ALTER SESSION SET container=PDBORCL;

ALTER SESSION SET "_ORACLE_SCRIPT"=true;

CREATE USER jones IDENTIFIED BY welcome;

Show parameter os_authent_prefix;

SHOW con_name;

HOST whoami;

CREATE USER “OPS$PLABODB01\ADMINISTRATOR” IDENTIFIED EXTERNALLY;

GRANT CREATE SESSION TO “OPS$PLABODB01\ADMINISTRATOR”;

Exit

Sqlplus /@pdborcl

Show user;

CREATE USER spy IDENTIFIED GLOBALLY
AS ‘CN=spy, OU=tier, O=security, C=US’;



## Assigning tablespace and quotas 

CREATE USER meltingstone IDENTIFIED BY passworduser
DEFAULT TABLESPACE users;

ALTER USER meltingstone
DEFAULT TABLESPACE users;

CREATE USER rover IDENTIFIED BY sweet
DEFAULT TABLESPACE users
TEMPORARY TABLESPACE temp;

ALTER USER rover
TEMPORARY TABLESPACE temp;

ALTER DATABASE DEFAULT TEMPORARY TABLESPACE temp;

CREATE USER dchip IDENTIFIED BY “secret”
QUOTA 100M ON USERS;

ALTER USER dchip
QUOTA UNLIMITED ON USERS;

GRANT CREATE SESSION, CREATE TABLE to simon IDENTIFIED BY simonpwd;

CONNECT simon/simonpwd@PLABDB01:1521/PDBORCL;

CREATE TABLE table1 (column1 NUMBER);

INSERT INTO table1 VALUES (4);


## Removing a user

ALTER SESSION SET container=PDBORCL;

ALTER SESSION SET "_ORACLE_SCRIPT"=true;

DROP USER jones CASCADE;



## Managing defaults 

ALTER USER rover PASSWORD EXPIRE ACCOUNT LOCK;


## Granting Privileges 

CREATE USER sales_man IDENTIFIED BY Passw0rd;

GRANT SELECT, INSERT, UPDATE, DELETE ON hr.employees TO sales_man;

GRANT SELECT ON hr.employees TO public;

GRANT SELECT ON hr.employees TO sales_man WITH GRANT OPTION;



## System privileges 

CREATE USER appl_data IDENTIFIED BY Passw0rd;

GRANT create user, alter user, drop user TO appl_data;

GRANT flashback any table TO public;

GRANT CREATE ANY INDEX TO appl_data WITH ADMIN OPTION;




## Creating and managing roles

CREATE ROLE appldba;

SET ROLE appldba IDENTIFIED BY secret;


CREATE USER charlie IDENTIFIED BY Passw0rd;

GRANT oem_monitor TO charlie;

GRANT select_catalog_role TO public;

GRANT CREATE ANY INDEX TO appldba WITH ADMIN OPTION;

CREATE ROLE hradmin;

CREATE ROLE employee;

SET ROLE hradmin IDENTIFIED BY “mysecret”, employee;

SET ROLE ALL EXCEPT hradmin;

CREATE USER johnny IDENTIFIED BY pleasure;
CREATE ROLE work;
GRANT CREATE SESSION, CREATE ROLE, ALTER USER TO johnny;
GRANT WORK TO johnny;
CONNECT johnny/pleasure@PDBORCL

SELECT role FROM session_roles;

SELECT granted_role
FROM user_role_privs
WHERE username IN (USER, ‘PUBLIC’);

SELECT role FROM session_roles
INTERSECT
SELECT granted_role FROM user_role_privs
WHERE username IN (USER, ‘PUBLIC’);

CREATE ROLE employee1;
GRANT employee1 TO scott;
ALTER USER scott DEFAULT ROLE ALL EXCEPT resource;



## Using defualts 

CONNECT / as sysdba
SELECT grantee, privilege, admin_option
FROM dba_sys_privs
WHERE grantee = ‘RESOURCE’;

ALTER SESSION SET container=PDBORCL;
REVOKE EXECUTE ON utl_smtp FROM PUBLIC;

REVOKE EXECUTE ON dbms_obfuscation_toolkit FROM PUBLIC;



## Assigning profile and account setting

Exit

Sqlplus / as sysdba
ALTER SESSION SET "_ORACLE_SCRIPT"=true;

CREATE PROFILE adminuser_profile LIMIT
SESSIONS_PER_USER 2
IDLE_TIME 5
CONNECT_TIME 10;

CREATE USER joel IDENTIFIED BY “knowhow”
DEFAULT TABLESPACE users
TEMPORARY TABLESPACE temp
PROFILE adminuser_profile;

ALTER SESSION SET container=PDBORCL;
CREATE USER gold IDENTIFIED BY silver PASSWORD EXPIRE;

GRANT connect, resource TO gold;

Exit
Sqlplus gold/silver@PDBORCL

SHOW user



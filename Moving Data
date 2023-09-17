## Directory Objects

Connect scott/tiger@pdborcl
create directory scott_demo as 'C:\scott\';
connect /@pdborcl as sysdba
grant create any directory to scott;
connect scott/tiger@pdborcl
create directory scott_demo as 'C:\scott\';
grant write on directory scott_demo to hr;
select * from all_directories where directory_name = 'SCOTT_DEMO'; drop directory scott_demo;
connect /@pdborcl as sysdba
drop directory scott_demo;


## Using SQL loader to load data from non-oracle database 

connect scott/tiger@pdborcl

desc salgrade;

SELECT * FROM salgrade;

6,4001,8999
7,4501,7999
8,5001,9999

Load data
Infile 'C:\sqlldr\sal.dat'
Badfile 'sals.bad'
Discardfile 'sals.dsc'
Append
Into table salgrade
Fields terminated by ','
Trailing nullcols
(grade,
losal,
hisal)

exit

sqlldr userid=scott/tiger@pdborcl control=C:\sqlldr\sals.ctl

Connect scott/tiger@pdborcl
SELECT * FROM salgrade;


## Using Data Pump to moe data between oracle databases

CREATE OR REPLACE DIRECTORY dir_demo AS
'C:\test\';
Grant read on directory dir_demo to scott;
Grant write on directory dir_demo to scott;
Grant imp_full_database to scott;
EXPDP scott/tiger@pdborcl
TABLES=SALGRADE
DIRECTORY=DIR_DEMO
DUMPFILE=SALES.dmp
LOGFILE=expdpsal.log

EXPDP scott/tiger@pdborcl
SCHEMAS=SCOTT
DIRECTORY=DIR_DEMO
DUMPFILE= SCOTT.dmp
LOGFILE=expdpSCOTT.log



## IMPDP

Sqlplus scott/tiger@pdborcl;
Drop table salgrade;
Exit

IMPDP scott/tiger@pdborcl
TABLES=SALGRADE
DIRECTORY=DIR_DEMO
DUMPFILE=SALES.dmp
LOGFILE impdpsal.log

Sqlplus /@pdborcl as sysdba
Create user scott_imp identified by tiger default tablespace USERS quota unlimited on USERS;
grant connect,resource to scott_imp;

IMPDP scott/tiger@pdborcl
SCHEMAS=SCOTT
DIRECTORY= DIR_DEMO
REMAP_SCHEMA=SCOTT:SCOTT_IMP
DUMPFILE=SCOTT.dmp
LOGFILE=impdpSCOTT.log


## Data pump with command line 

Sqlplus / as sysdba
Alter user system identified by manager;

CREATE OR REPLACE DIRECTORY DATA_DEMO AS 'C: \datap\';
GRANT READ, WRITE ON DIRECTORY DATA_DEMO TO SYSTEM;
GRANT imp_full_database TO system;

Expdp system/manager@pdborcl full=y
Parallel=2
directory=data_demo
Dumpfile=full_%U.dmp
Filesize=2g
Compression=all
logfile=fulllog.log

Impdp system/manager@pdborcl full=y
Directory=data_demo
Parallel=2
Dumpfile=full_%U.dmp
logfile=fulllog.log

CREATE DIRECTORY codes AS 'C:\code\';

Expdp system/manager@pdborcl schemas=hr, oe
Directory=codes
Dumpfile=hr_oe_code.dmp
Include=function,
Include=package,
Include=procedure,
Include=type

Sqlplus /@pdborcl as sysdba
Create user dev identified by dev default tablespace USERS quota unlimited on USERS;
grant connect, resource to dev;

CREATE DIRECTORY userdata AS 'C: \userdemo\';
Grant read, write on directory userdata to dev;
grant imp_full_database to dev;


Expdp system/manager@pdborcl directory=userdata dumpfile=userdata.dmp schemas=hr logfile=userdata.log

impdp system/manager@pdborcl
Directory=userdata
Dumpfile=userdata.dmp
schemas=hr
remap_schema=hr:dev



## Loading external table using Data Pump

CREATE DIRECTORY SCOTTTEST AS 'C:\scottdemo\';

Create table sales_demo
(grade number,
losal number,
hisal number )
organization external (
type oracle_loader
default directory scotttest
access parameters
(records delimited by newline
badfile 'sals.bad'
discardfile 'sals.dsc'
logfile 'sals.log'
fields terminated by ','
missing field values are null)
location ('C:\sqlldr\sal.dat'));

DESC sales_demo;


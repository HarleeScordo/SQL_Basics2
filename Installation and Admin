## Instance Start-up 

SHUTDOWN IMMEDIATE;
STARTUP MOUNT;
ALTER DATABASE ARCHIVELOG;
ALTER DATABASE OPEN;
ARCHIVE LOG LIST;
SELECT log_mode FROM V$DATABASE;

SHUTDOWN IMMEDIATE;


STARTUP

SELECT file#, error FROM V$RECOVER_FILE;

SELECT file#, name
FROM V$DATAFILE
JOIN
V$RECOVER_FILE
USING (file#);

SHUTDOWN IMMEDIATE;

STARTUP;


## Using Flashback Query

SHOW con_name;

SHOW USER;
GRANT FLASHBACK ANY TABLE TO OE;
GRANT EXECUTE ON DBMS_FLASHBACK TO OE;

CONN oe/oe@pdborcl;

SELECT employee_id, last_name, email
FROM employees
AS OF TIMESTAMP (systimestamp - interval '15' minute)
WHERE employee_id=101;

SELECT employee_id, last_name, email
FROM employees
AS OF TIMESTAMP (to_timestamp ('22-Aug-16 16:00:57.845993', 'DD-MON-RR HH24: MI: SS.FF'))
WHERE employee_id=101;

SELECT employee_id, last_name, email
FROM employees
AS OF TIMESTAMP (systimestamp - interval '10' month)
WHERE employee_id=101;


## Flashback drop and recycle 

SELECT order_id, line_item_id, product_id
FROM order_items
WHERE rownum < 5;

Drop table order_items;

select order_id, line_item_id, product_id
from order_items
where rownum < 5;

FLASHBACK table order_items to before drop;

SELECT order_id, line_item_id, product_id
FROM order_items
WHERE rownum < 5;

Drop table order_items;

Flashback table order_items to before drop rename to order_items_old_version;

SELECT order_id, line_item_id, product_id
FROM order_items_old_version
WHERE rownum < 5;


## Flashback table 

CONNECT hr/hr@pdborcl
ALTER TABLE employees ENABLE ROW MOVEMENT;
ALTER TABLE departments ENABLE ROW MOVEMENT;

SELECT * FROM departments WHERE department_name = 'IT';

FLASHBACK TABLE employees, departments
TO TIMESTAMP systimestamp - interval '15' minute;

SELECT * FROM departments WHERE department_name='IT';

FLASHBACK TABLE employees
TO TIMESTAMP TO_TIMESTAMP ('22AUG16 22:00', 'DDMONYY HH24: MI') ENABLE TRIGGERS;


Recovering a Control File Loss

SHOW PARAMETER control_files;

SHUTDOWN IMMEDIATE
Exit

STARTUP MOUNT

SELECT name, value FROM v$spparameter WHERE name = 'control_files';

ALTER SYSTEM SET control_files =
'C:\app\DBAdmin\oradata\orcl\control01.ctl' SCOPE=SPFILE;

SHUTDOWN IMMEDIATE;

STARTUP

SELECT name, value FROM V$SPPARAMETER WHERE name = 'control_files';



## Recovering from loss of a Redo log file

SELECT group#, member FROM v$logfile;

SHUTDOWN IMMEDIATE;

STARTUP MOUNT;

SELECT group#, member, status FROM v$logfile;

SELECT group#, status, members, archived FROM v$log;

ALTER DATABASE CLEAR LOGFILE GROUP 2;

ALTER DATABASE OPEN;

Alter database open resetlogs;


### Recovering from loss of Non-System Critical Data File

rman target /

backup database;

ALTER DATABASE DATAFILE
'C:\app\DBAdmin\oradata\orcl\users01.dbf' OFFLINE;

SHUTDOWN IMMEDIATE;

STARTUP MOUNT;

ALTER DATABASE DATAFILE
'C: \app\DBAdmin\oradata\orcl\users01.dbf' ONLINE;

ALTER DATABASE OPEN;

CREATE TABLE t1 tablespace users AS select * from dba_tables;

rman target /

List failure;

advise failure all;

repair failure;

CHANGE FAILURE 2 PRIORITY LOW;
CHANGE FAILURE 5 CLOSE;

CREATE TABLE t1 TABLESPACE users AS SELECT * FROM dba_tables;


## Recovering from a loss of a system critical data file

SHUTDOWN ABORT;

STARTUP

rman target /

Restore datafile 1;

Recover database;

Alter database open;



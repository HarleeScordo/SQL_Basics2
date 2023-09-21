# SQL_Basics2
## In command prompt - Sqlplus / as sysdba

{
## Display Parameter values:
SELECT name, value
FROM v$parameter
WHERE name LIKE '%max_size%';

SELECT name, value, display_value, isspecified
FROM v$spparameter
WHERE name LIKE '%max_size%';

show parameter pool_size;
}

{
## Difference between V$PARAMETER and V$SPPARAMETER 
##The V$PARAMETER view shows which initialization parameters are currently in effect. This view has several useful columns such as NAME, VALUE, DISPLAY_VALUE, DESCRIPTION, ISBASIC, ISDEFAULT and ISMODIFIED.
##The V$SPPARAMETER shows the contents of the SPFILE used to start the instance. If the value in the ISSPECIFIED column is FALSE, then the instance was started using PFILE.

SELECT name, value
FROM v$parameter
WHERE name LIKE 'control%'
AND isdefault = 'FALSE';

SELECT name, value
FROM v$spparameter
WHERE name LIKE 'control%'
AND isspecified = 'TRUE';
}

{
### Change Parameter values 

alter session
set nls_date_format = 'DD-MON-YYYY HH24: MI: SS';

alter system
set undo_retention = 3600 scope = memory;

alter system
set undo_management = manual;

alter system
set undo_management = manual scope = spfile;

alter system
set sga_target = 500M scope = both;

show parameter undo;

show spparameter undo;
}

{
## Starting an Oracle Database Instance
Startup force pfile = 'c:\app\DBAdmin\admin\orcl\pfile\init.ora.35201664723';

## Shutting down Oracle 
Shutdown immediate;
}

{
## Dynamic Performance View
SELECT name, value
FROM v$parameter
WHERE name LIKE 'resource%';

SELECT username, status, osuser, sid, serial#
FROM v$session
WHERE username = 'SYS';
}



## Using Compression

CREATE TABLE secure COMPRESS NOLOGGING
AS SELECT * FROM securing WHERE 1=2;

INSERT /*+ APPEND */ INTO secure
SELECT * FROM securing;

CREATE TABLE secure_oltp COMPRESS FOR OLTP
AS SELECT * FROM securing WHERE 1=2;

CREATE TABLE secure_oltp COMPRESS FOR OLTP
AS SELECT * FROM securing WHERE 1=2;



## Monitoring free space 

WITH
ts_free AS (SELECT tablespace_name, round (sum (bytes)/1048576) free_mb FROM dba_free_space GROUP BY tablespace_name),
ts_alloc AS (SELECT tablespace_name, round (sum (bytes)/1048576) alloc_mb FROM dba_data_files GROUP BY tablespace_name)
SELECT tablespace_name, status, alloc_mb,
free_mb, alloc_mb-free_mb used_mb
FROM dba_tablespaces
JOIN ts_alloc USING (tablespace_name)
JOIN ts_free USING (tablespace_name)
/


WITH
ts_free AS (SELECT tablespace_name, round (sum (bytes)/1048576) free_mb FROM dba_free_space GROUP BY tablespace_name),
ts_alloc AS (SELECT tablespace_name, round (sum ((decode (autoextensible, ’YES’, maxbytes, bytes))/1048576) alloc_mb
FROM dba_data_files GROUP BY tablespace_name)
SELECT tablespace_name, status, alloc_mb, free_mb, alloc_mb-free_mb used_mb
FROM dba_tablespaces t
JOIN ts_alloc al USING (tablespace_name)
JOIN ts_free f USING (tablespace_name)
/


## Notifying on session suspend

SELECT user_id, session_id, status, timeout, suspend_time, error_number, sql_text
FROM DBA_RESUMABLE
/

Alter session enable resumable timeout 300;

CREATE TABLE demo1 AS SELECT * FROM dba_objects;

insert into data1 select * from dba_objects;

CREATE OR REPLACE TRIGGER resumable_def_timeout
AFTER SUSPEND
ON DATABASE
BEGIN
DBMS_RESUMABLE.SET_TIMEOUT (10800);
END;
/


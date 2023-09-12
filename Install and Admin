## Manage Listeners

lsnrctl

help

help show 

stop listener

start listener

Reload listener 

Status 

Services Listener 

## Network Management

(DESCRIPTION=
(ADDRESS= (PROTOCOL= tcp) (HOST= PLABODB01) (PORT= 1521)
(CONNECT_DATA=
(SERVICE_NAME= PDBORCL)
(SERVER= SHARED)
(INSTANCE_NAME= ORCL))))


## Shared Server 

ALTER SYSTEM
SET DISPATCHERS = “(PRO= TCP) (DIS= 3) (PRO= IPC) (DIS= 2)”;

ALTER SYSTEM SET DISPATCHERS = “(PRO= TCP) (DIS= 10)”;

SELECT sid, serial#, username, server, program
FROM V$SESSION
WHERE username IS NOT NULL;

ALTER SYSTEM SET DISPATCHERS= “(PRO= TCP) (DIS= 5)”;

ALTER SYSTEM
SET DISPATCHERS = “(PROTOCOL=tcp) (DISPATCHER=1) (POOL=on) (TICK=1) (CONNECTIONS=500) (SESSIONS=1000)”;

ALTER SYSTEM SET MAX_DISPATCHERS = 10;

ALTER SYSTEM SET SHARED_SERVERS = 5;

ALTER SYSTEM SET SHARED_SERVER_SESSIONS = 5;

ALTER SYSTEM SET MAX_SHARED_SERVERS = 20;

ALTER SYSTEM SET CIRCUITS = 300;



## Comms between Databases 

Execute dbms_connection_pool.start_pool;

SELECT * FROM dba_cpool_info;

##Links
CREATE PUBLIC DATABASE LINK db_link01 USING ‘pdborcl’;



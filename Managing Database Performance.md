## Collecting Stats

BEGIN
DBMS_AUTO_TASK_ADMIN.DISABLE (
client_name=> 'auto optimizer stats collection',
operation=> NULL,
window_name=> NULL);
END;
/

SELECT client_name, status FROM dba_autotask_client;

BEGIN
DBMS_AUTO_TASK_ADMIN.ENABLE (
client_name=>'auto optimizer stats collection',
operation=> NULL,
window_name=> NULL);
END;
/

SELECT client_name, status FROM dba_autotask_client;

SELECT client_name, status FROM dba_autotask_client;

BEGIN
DBMS_STATS.GATHER_TABLE_STATS ('HR', 'COUNTRIES'); DBMS_STATS.LOCK_TABLE_STATS ('HR', 'COUNTRIES');
END;
/

EXEC DBMS_STATS.GATHER_SCHEMA_STATS (ownname => 'HR', estimate_percent=>50);



## Gathering Performance Stats

Execute dbms_workload_repository.modify_snapshot_settings (interval=>60, retention=>43200);

Exec dbms_workload_repository.modify_snapshot_settings (43200, 30);

set ORACLE_HOME=C:\app\DBAdmin\product\12.1.0\dbhome_1
Cd
cd app\DBAdmin\product\12.1.0\dbhome_1\rdbms\admin
Sqlplus / as sysdba

@awrrpt.sql

@awrddrpt.sql

EXECUTE DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT ();

Col begin_interval_time for a22;
Col end_interval_time for a22;
SELECT snap_id, begin_interval_time, end_interval_time
FROM dba_hist_snapshot
ORDER BY snap_id;

BEGIN
DBMS_WORKLOAD_REPOSITORY.DROP_SNAPSHOT_RANGE (8, 10);
END;
/


AWR

BEGIN
DBMS_WORKLOAD_REPOSITORY.CREATE_BASELINE (
start_snap_id=>14,
end_snap_id=>16,
baseline_name=>'Baseline Example',
expiration=>21);
END;
/


BEGIN
DBMS_WORKLOAD_REPOSITORY.DROP_BASELINE (
baseline_name=>'Baseline Example',
cascade=>FALSE);
END;
/


BEGIN
DBMS_WORKLOAD_REPOSITORY.CREATE_BASELINE_TEMPLATE (
start_time=> SYSDATE,
end_time=> SYSDATE+4,
baseline_name => 'baseline_010916',
template_name => 'template_010916',
expiration=> 21);
END;
/


BEGIN
DBMS_WORKLOAD_REPOSITORY.CREATE_BASELINE_TEMPLATE (
day_of_week => 'Friday',
hour_in_day => 9,
duration => 3,
start_time => SYSDATE+1,
end_time => ADD_MONTHS (SYSDATE, 24),
baseline_name_prefix => 'baseline_FRI_',
template_name => 'template_FRI’,
expiration => 60);
END;
/


## Data Dictionaries and ADDM Analysis 

Col message for a40;
SELECT task_id, type, message FROM dba_advisor_findings
WHERE impact = (SELECT MAX (impact) FROM dba_advisor_findings);

SELECT attr4 FROM dba_advisor_objects WHERE task_id = 1;

SELECT message FROM dba_advisor_rationale WHERE task_id = 1;


## Viewing configuring alerts using SQL

SELECT metrics_name, warning_value, critical_value
FROM dba_thresholds WHERE metrics_name like 'Tablespaces%';

SELECT reason FROM dba_outstanding_alerts;


## ADRCI to view Alert Log File

Col name for a33;
Col value for a33;
SELECT name, value FROM v$diag_info;

adrci

help

Help show alert



## Auto shared memory management

SELECT name, bytes FROM v$sgainfo;

SELECT * FROM v$sga_target_advice;



## Auto Memory Management

SELECT memory_size, memory_size_factor, estd_db_time, estd_db_time_factor FROM v$memory_target_advice;

Col component for a33;
SELECT component, current_size, min_size, max_size
FROM v$memory_dynamic_components;


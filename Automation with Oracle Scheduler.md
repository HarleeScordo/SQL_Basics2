## Granting scheduler privileges

GRANT SCHEDULER_ADMIN TO demo;

SELECT grantee, privilege FROM dba_sys_privs
WHERE grantee = 'SCHEDULER_ADMIN';


SELECT distinct privilege from dba_sys_privs
WHERE privilege LIKE '%JOB%'
OR privilege LIKE '%SCHED%' ORDER BY 1;


## Job states

Col owner for a22;
Col job_name for a33;
SELECT owner, job_name state FROM dba_scheduler_jobs;


## Schedules

BEGIN
DBMS_SCHEDULER.CREATE_SCHEDULE (
schedule_name => 'SCHEDULEDEMO',
start_date => SYSDATE,
repeat_interval => 'FREQ = HOURLY; BYMINUTE = 45',
end_date => SYSDATE+1);
END;
/


Exec dbms_scheduler.drop_scheduler (
schedule_name => 'scheduledemo');


BEGIN
DBMS_SCHEDULER.SET_SCHEDULE_ATTRIBUTE
(attribute => 'LOG_HISTORY', value => '5');
END;
/


SET SERVEROUTPUT ON;
DECLARE
locVALUE INTEGER;
BEGIN
DBMS_SCHEDULER.GET_SCHEDULE_ATTRIBUTE
('LOG_HISTORY', locVALUE);
DBMS_OUTPUT.PUT_LINE ('LOG_HISTORY='||locVALUE);
END;
/


## Create Program 

SELECT table_name FROM dictionary
WHERE table_name LIKE 'DBA_SCHED%'
OR table_name LIKE 'V$SCHED%' ORDER BY table_name;


BEGIN
DBMS_SCHEDULER.CREATE_PROGRAM (
Program_name => 'p_demo',
program_action => 'pactiondemo',
program_type => 'stored_procedure',
number_of_arguments => 1,
enabled => false,
comments => 'program created');
END;
/


BEGIN
dbms_scheduler.DEFINE_PROGRAM_ARGUMENT (
program_name=>'p_demo',
argument_name=>'argument_name',
argument_position=>1,
argument_type=>'varchar2');
END;
/


BEGIN
dbms_scheduler.enable (name => 'p_demo');
END;
/


BEGIN
DBMS_SCHEDULER.CREATE_JOB (
job_name => 'job_demo', program_name => 'p_demo',
repeat_interval => 'FREQ=HOURLY; BYMINUTE=45', enabled => false);
END;
/


BEGIN
dbms_scheduler.set_job_argument_value (
job_name=>'job_demo',
argument_position=> 1,
argument_value=>'testvalue');
END;
/


## Jobs

CREATE OR REPLACE PROCEDURE jdemo AS
BEGIN
DBMS_OUTPUT.PUT_LINE ('JOBSDEMO');
END;
/


SET SERVEROUTPUT ON;
BEGIN
DBMS_SCHEDULER.CREATE_JOB (
job_name => 'jobtest',
job_type => 'STORED_PROCEDURE',
job_action => 'jdemo');
END;
/


BEGIN
DBMS_SCHEDULER.RUN_JOB (job_name => 'jobtest');
END;
/


BEGIN
DBMS_SCHEDULER.STOP_JOB (job_name => 'jobtest');
END;
/


BEGIN
DBMS_SCHEDULER.DROP_JOB (job_name => 'jobtest');
END;
/


## Job Classes

Exec DBMS_SCHEDULER.CREATE_JOB_CLASS (
job_class_name => 'jobclassdemo');


Exec DBMS_SCHEDULER.DROP_JOB_CLASS (
job_class_name => 'jobclassdemo');


## Groups 

BEGIN
DBMS_SCHEDULER.CREATE_GROUP (
group_name => 'groupdemo',
group_type => 'DB_DEST',
member => 'LOCAL');
END;
/


## Window

BEGIN
DBMS_SCHEDULER.CREATE_WINDOW (
window_name => 'window_demo',
resource_plan => NULL,
schedule_name => 'scheduledemo',
duration => INTERVAL '60' MINUTE,
window_priority => 'LOW');
END;
/



## Job Chains to perform a series of related tasks

Exec DBMS_SCHEDULER.CREATE_CHAIN (chain_name => 'chaindemo');


BEGIN
DBMS_SCHEDULER.DEFINE_CHAIN_STEP (chain_name => 'chaindemo', step_name => 'stepdemo1', program_name => 'program_demo'); 
DBMS_SCHEDULER.DEFINE_CHAIN_STEP (chain_name => 'chaindemo', step_name => 'stepdemo2', program_name => 'program_demo');
END;
/


Exec DBMS_SCHEDULER.ALTER_CHAIN ('chaindemo', 'stepdemo2', 'SKIP', TRUE);


Exec DBMS_SCHEDULER.ENABLE (name => 'chaindemo');


Exec DBMS_SCHEDULER.DISABLE (name => 'chaindemo');


Desc dba_scheduler_running_chains;


BEGIN
DBMS_SCHEDULER.CREATE_JOB (
job_name => 'chainjob',
job_type => 'chain',
job_action => 'chaindemo',
repeat_interval => 'freq=hourly; byminute=45',
enabled => TRUE);
END;
/


BEGIN
DBMS_SCHEDULER.RUN_CHAIN (
chain_name => 'chaindemo', start_steps => 'stepdemo1, stepdemo2');
END;
/


Exec DBMS_SCHEDULER.STOP_JOB (job_name => 'chainjob');


Exec DBMS_SCHEDULER.DROP_JOB (job_name => 'chainjob');


Exec DBMS_SCHEDULER.DROP_CHAIN (chain_name => 'chaindemo');


SELECT table_name FROM dictionary
WHERE table_name LIKE 'DBA%CHAIN%' OR table_name LIKE 'V$CHAIN%';


## Scheduling Jobs on Remote systems 

BEGIN
DBMS_SCHEDULER.CREATE_DATABASE_DESTINATION (
destination_name => 'destdemo',
agent => 'agentdemo',
tns_name => 'pdborcl');
END;
/


BEGIN
DBMS_SCHEDULER.CREATE_CREDENTIAL (
credential_name => 'credemo',
username => 'userdemo',
password => 'usercre',
database_role => SYSTEM);
END;
/


BEGIN
DBMS_SCHEDULER.CREATE_JOBS (
job_name => 'test',
job_type => 'STORED_PROCEDURE',
job_action => 'chaindemo',
credential_name => 'credemo',
destination_name => 'destdemo');
END;
/


### Prioritizing jobs with Oracle Scheduler 

SELECT table_name FROM dictionary
WHERE table_name LIKE 'DBA%CONSUMER%';


BEGIN
DBMS_SCHEDULER.CREATE_JOB_CLASS (
job_class_name => 'jobdemo1',
resource_consumer_group => 'ETL_GROUP');
END;
/


BEGIN
DBMS_SCHEDULER.CREATE_JOB (
job_name => 'jobtest1',
job_type => 'STORED_PROCEDURE',
job_action => 'chaindemo');
END;
/


BEGIN
DBMS_SCHEDULER.SET_ATTRIBUTE (
name => 'jobtest1',
attribute => 'job_priority',
value => 1);
END;
/


SELECT owner, job_name, program_name, job_priority FROM dba_scheduler_jobs ORDER BY 4;



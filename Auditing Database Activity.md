## Managing Statement Auditing 

Alter system set audit_trail=DB scope=spfile;

AUDIT table;

AUDIT table BY compass;

AUDIT table BY compass WHENEVER NOT SUCCESSFUL;

AUDIT INSERT TABLE BY compass BY ACCESS;

SELECT audit_option, failure, success, user_name
FROM dba_stmt_audit_opts
ORDER BY audit_option, user_name;

NOAUDIT session;
NOAUDIT not exists;
NOAUDIT table BY compass;

SELECT username, timestamp, action_name
FROM dba_audit_trail
WHERE username='compass';



## Managing Privilege Auditing 

AUDIT CREATE ANY TABLE;

AUDIT CREATE ANY TABLE BY compass;

AUDIT DELETE ANY TABLE BY compass BY ACCESS;

SELECT privilege, user_name
FROM dba_priv_audit_opts
ORDER BY privilege, user_name;

NOAUDIT alter profile;
NOAUDIT delete any table BY compass;
NOAUDIT alter user by compass;



## Managing Object Auditing 

AUDIT SELECT ON emp_sal;

AUDIT SELECT ON emp_sal
BY ACCESS WHENEVER SUCCESSFUL;

AUDIT SELECT ON emp_sal
BY SESSION WHENEVER NOT SUCCESSFUL;

SELECT owner, object_name, object_type, ins, sel
FROM dba_obj_audit_opts
WHERE owner='SYS'
AND object_name='EMP_SAL';

NOAUDIT select ON emp_sal WHENEVER NOT SUCCESSFUL;



## Purging the Audit Trail 

SELECT tablespace_name FROM dba_tables WHERE table_name='AUD$';

EXECUTE DBMS_AUDIT_MGMT.SET_AUDIT_TRAIL_LOCATION ( - AUDIT_TRAIL_TYPE => DBMS_AUDIT_MGMT.AUDIT_TRAIL_AUD_STD, - AUDIT_TRAIL_LOCATION_VALUE => 'SYSAUX');

SELECT tablespace_name FROM dba_tables WHERE table_name='AUD$';



## Using the Audit Management 

SELECT * FROM DBA_AUDIT_MGMT_CONFIG_PARAMS;



## Creating FGA Policy 

EXECUTE DBMS_FGA.ADD_POLICY (object_schema=>'HR', -
object_name=>'EMPLOYEES', -
policy_name=>'DEMO', -
audit_column=>'SALARY, COMMISSION_PCT', -
enable=>FALSE, -
statement_types=>'SELECT');



## Enabling an FGA Policy 

EXECUTE DBMS_FGA.ENABLE_POLICY (object_schema=>'HR', - object_name=>'EMPLOYEES', -
policy_name=>'DEMO');

## Disabling 

EXECUTE DBMS_FGA.DISABLE_POLICY (object_schema=>'HR', - object_name=>'EMPLOYEES', -
policy_name=>'DEMO');


## Dropping FGA

EXECUTE DBMS_FGA.DROP_POLICY (object_schema=>'HR', - object_name=>'EMPLOYEES', -
policy_name=>'DEMO');


## ID FGA

SELECT policy_name, object_schema||''|| object_name
object_name,
policy_column,
enabled ,audit_trail
FROM dba_audit_policies;


## Reporting on FGA Audit Trail 

SELECT db_user, timestamp, userhost
FROM dba_fga_audit_trail
WHERE policy_name='DEMO';



## Creating a Unified Audit Policy 

CREATE AUDIT POLICY ocp_policy
PRIVILEGES SELECT ANY TABLE, ALTER SESSION;

CREATE AUDIT POLICY action_demo
ACTIONS execute, alter database link;

CREATE AUDIT POLICY role_demo ROLES olap_dba, resource;

CREATE AUDIT POLICY multi_policy
PRIVILEGES SELECT ANY TABLE, ALTER SESSION
ACTIONS execute, alter database link
ROLES olap_dba, resource;

CREATE AUDIT POLICY table_demo
ACTIONS delete, insert ON departments;

CREATE AUDIT POLICY policy_demo
ACTIONS delete, insert, update ON employees,
delete, insert, update ON departments
WHEN 'INSTR (SYS_CONTEXT (''USERENV'', ''SESSION_USER''), ''ODBC'')=1'
EVALUATE PER SESSION;



## Using component Auditing 

SELECT component, count (*) actions
FROM auditable_system_actions
GROUP BY component
ORDER BY component;

CREATE AUDIT POLICY datapump_demo
ACTIONS COMPONENT=datapump export;



## Enabling and Disabling Audit Policies 

AUDIT POLICY policy_demo;

AUDIT POLICY ocp_policy
BY Thomas, Joshua WHENEVER NOT SUCCESSFUL;

AUDIT POLICY table_demo
EXCEPT jenny WHENEVER SUCCESSFUL;

NOAUDIT POLICY ocp_policy;



## Querying Audit Info and Audit Policies 

select UNIFIED_AUDIT_POLICIES, DBUSERNAME,
ACTION_NAME, SYSTEM_PRIVILEGE_USED
from UNIFIED_AUDIT_TRAIL
where UNIFIED_AUDIT_POLICIES like ’%SECURE%’;

Select DBUSERNAME, DP_TEXT_PARAMETERS1, DP_BOOLEAN_PARAMETERS1
from UNIFIED_AUDIT_TRAIL
where DBUSERNAME = 'SYSTEM';

select OS_USERNAME, RMAN_OBJECT_TYPE, RMAN_OPERATION
from UNIFIED_AUDIT_TRAIL
where RMAN_OPERATION is not null;

SELECT policy_name, audit_option, audit_option_type, audit_condition
FROM audit_unified_policies
WHERE audit_option like '%update%';

SELECT user_name, policy_name, enabled_opt, success, failure
FROM audit_unified_enabled_policies;


## Purging Unified Audit Records 

delete from unified_audit_trail;

REM Enable Automated Audit Trail Cleanup - 60 days
set serveroutput on
REM Set Archive TimeStamp - delete records older than 60 days
begin
dbms_audit_mgmt.set_last_archive_timestamp (
audit_trail_type => dbms_audit_mgmt.AUDIT_TRAIL_UNIFIED,
last_archive_time => systimestamp-60);
end;
/

begin
dbms_audit_mgmt.clean_audit_trail (
audit_trail_type => dbms_audit_mgmt.AUDIT_TRAIL_UNIFIED,
use_last_arch_timestamp => true);
end;
/

begin
dbms_audit_mgmt.create_purge_job (
audit_trail_type => dbms_audit_mgmt.AUDIT_TRAIL_UNIFIED,          
audit_trail_purge_interval => 24 /* 1 day */ ,          
audit_trail_purge_name => 'PURGE_DB_AUDIT_RECORDS',          
use_last_arch_timestamp => true);
end;
/

begin
dbms_scheduler.create_job (
job_name => 'JDEMO',
job_type => 'PLSQL_BLOCK',
job_action => 'DBMS_AUDIT_MGMT.set_last_archive_timestamp (
audit_trail_type => DBMS_AUDIT_MGMT.AUDIT_TRAIL_AUD_STD,
last_archive_time => SYSTIMESTAMP-60);',
start_date => systimestamp,
repeat_interval => 'freq=daily; byhour=20',
end_date => null,
enabled => true,
comments => 'abcde');
end;
/


## Unified Auditing Internals 

EXEC DBMS_AUDIT_MGMT.SET_AUDIT_TRAIL_PROPERTY (-      
DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED, -      
DBMS_AUDIT_MGMT.AUDIT_TRAIL_WRITE_MODE, -      
DBMS_AUDIT_MGMT.AUDIT_TRAIL_IMMEDIATE_WRITE);

EXEC DBMS_AUDIT_MGMT.SET_AUDIT_TRAIL_PROPERTY (-
DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED, -
DBMS_AUDIT_MGMT.AUDIT_TRAIL_WRITE_MODE, -
DBMS_AUDIT_MGMT.AUDIT_TRAIL_QUEUED_WRITE);

EXEC SYS.DBMS_AUDIT_MGMT.FLUSH_UNIFIED_AUDIT_TRAIL;

SELECT table_name FROM dba_tables
WHERE owner = 'AUDSYS';


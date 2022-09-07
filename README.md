# AWS Schema Conversion Tool and Database Migration Service

## 1. Prepare Environment

### 1. Prepare source and target database

Refer to [Launch Custom AMI EC2](https://github.com/t2yijaeho/Custom-AMI-EC2-with-CloudFormation)


Refer to [Create RDS PostgreSQL](https://github.com/t2yijaeho/Amazon-RDS-PostgreSQL-with-AWS-CloudFormation)

### 2. Create Windows firewall inbound rule for Oracle TNS port(1521) using PowerShell script

1. Open the PowerShell console and run script

    ```PowerShell
    New-NetFirewallRule -DisplayName “Oracle TNS (TCP-in 1521)” -Profile @(‘Domain’, ‘Private’, 'Public') -Direction Inbound -Protocol TCP –LocalPort 1521 -Action Allow
    ```

    ```PowerShell
    PS C:\Users\Administrator> New-NetFirewallRule -DisplayName “Oracle TNS (TCP-in 1521)” -Profile @(‘Domain’, ‘Private’, 'Public') -Direction Inbound -Protocol TCP –LocalPort 1521 -Action Allow
    >>
    Name                  : {8897f95a-d712-4847-a70c-45177fe444a1}
    DisplayName           : Oracle TNS (TCP 1521)
    Description           :
    DisplayGroup          :
    Group                 :
    Enabled               : True
    Profile               : Domain, Private, Public
    Platform              : {}
    Direction             : Inbound
    Action                : Allow
    EdgeTraversalPolicy   : Block
    LooseSourceMapping    : False
    LocalOnlyMapping      : False
    Owner                 :
    PrimaryStatus         : OK
    Status                : The rule was parsed successfully from the store. (65536)
    EnforcementStatus     : NotApplicable
    PolicyStoreSource     : PersistentStore
    PolicyStoreSourceType : Local
    PS C:\Users\Administrator>
    ```

### 3. Grant Oracle database user privileges

1. Grant privileges for SCT and DMS using sysdba(System Database Administrator) role

    ```SQL
    -- PRIVILEGES FOR SCT
    GRANT SELECT ANY DICTIONARY TO HR;
    -- PRIVILEGES FOR DMS
    GRANT CREATE SESSION TO HR;
    GRANT SELECT ANY TRANSACTION TO HR;
    GRANT SELECT ON SYS.V_$ARCHIVED_LOG TO HR;
    GRANT SELECT ON SYS.V_$LOG TO HR;
    GRANT SELECT ON SYS.V_$LOGFILE TO HR;
    GRANT SELECT ON SYS.V_$LOGMNR_LOGS TO HR;
    GRANT SELECT ON SYS.V_$LOGMNR_CONTENTS TO HR;
    GRANT SELECT ON SYS.V_$DATABASE TO HR;
    GRANT SELECT ON SYS.V_$THREAD TO HR;
    GRANT SELECT ON SYS.V_$PARAMETER TO HR;
    GRANT SELECT ON SYS.V_$NLS_PARAMETERS TO HR;
    GRANT SELECT ON SYS.V_$TIMEZONE_NAMES TO HR;
    GRANT SELECT ON SYS.V_$TRANSACTION TO HR;
    GRANT SELECT ON SYS.V_$CONTAINERS TO HR;
    GRANT SELECT ON ALL_INDEXES TO HR;
    GRANT SELECT ON ALL_OBJECTS TO HR;
    GRANT SELECT ON ALL_TABLES TO HR;
    GRANT SELECT ON ALL_USERS TO HR;
    GRANT SELECT ON ALL_CATALOG TO HR;
    GRANT SELECT ON ALL_CONSTRAINTS TO HR;
    GRANT SELECT ON ALL_CONS_COLUMNS TO HR;
    GRANT SELECT ON ALL_TAB_COLS TO HR;
    GRANT SELECT ON ALL_IND_COLUMNS TO HR;
    GRANT SELECT ON ALL_ENCRYPTED_COLUMNS TO HR;
    GRANT SELECT ON ALL_LOG_GROUPS TO HR;
    GRANT SELECT ON ALL_TAB_PARTITIONS TO HR;
    GRANT SELECT ON SYS.DBA_REGISTRY TO HR;
    GRANT SELECT ON SYS.OBJ$ TO HR;
    GRANT SELECT ON DBA_TABLESPACES TO HR;
    ```

    ```SQL
      ⋮
    Grant succeeded.
      ⋮
    ```



### 3. Install AWS Schema Conversion Tool

## 2. AWS

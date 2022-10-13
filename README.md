# AWS Schema Conversion Tool and Database Migration Service

## 1. Prepare Environment

### 1. Prepare IDE, source and target database

1. For Cloud IDE refer to [AWS Cloud9](https://github.com/t2yijaeho/Docker-with-AWS-Cloud9)

2. For source database Refer to [Launch Amazon EC2 using Custom AMI](https://github.com/t2yijaeho/Custom-AMI-EC2-with-CloudFormation)

3. For target database Refer to [Create Amazon RDS PostgreSQL instance](https://github.com/t2yijaeho/Amazon-RDS-PostgreSQL-with-AWS-CloudFormation)

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

1. Grant privileges for SCT and DMS on SQL Client using sysdba(System Database Administrator) role

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

### 4. Install xdg-utils for SCT

1. Update apt database

    ```console
    sudo apt update
    ```

    ```console
    mspuser:~/environment $ sudo apt update
    Hit:1 https://download.docker.com/linux/ubuntu bionic InRelease
      ⋮
    3 packages can be upgraded. Run 'apt list --upgradable' to see them.
    mspuser:~/environment $ 
    ```

2. Install xdg-utils using apt

    ```console
    sudo apt -y install xdg-utils
    ```

    ```console
    mspuser:~/environment $ sudo apt -y install xdg-utils
    Reading package lists... Done
      ⋮
    0 upgraded, 1 newly installed, 0 to remove and 3 not upgraded.
    mspuser:~/environment $ 
    ```

### 5. Install AWS Schema Conversion Tool

Refer to [Installing AWS SCT](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Installing.html#CHAP_Installing.Procedure)

1. Download the compressed AWS SCT installer for Ubuntu

    ```console
    wget https://s3.amazonaws.com/publicsctdownload/Ubuntu/aws-schema-conversion-tool-1.0.latest.zip
    ```

    ```console
    mspuser:~/environment $ wget https://s3.amazonaws.com/publicsctdownload/Ubuntu/aws-schema-conversion-tool-1.0.latest.zip
    --2022-09-07 01:48:57--  https://s3.amazonaws.com/publicsctdownload/Ubuntu/aws-schema-conversion-tool-1.0.latest.zip
    Resolving s3.amazonaws.com (s3.amazonaws.com)... 52.217.195.128
    Connecting to s3.amazonaws.com (s3.amazonaws.com)|52.217.195.128|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 1023114636 (976M) [application/zip]
    Saving to: ‘aws-schema-conversion-tool-1.0.latest.zip’

    aws-schema-conversion-tool-1.0.late 100%[================================================================>] 975.72M  8.73MB/s    in 2m 28s  

    2022-09-07 01:51:26 (6.58 MB/s) - ‘aws-schema-conversion-tool-1.0.latest.zip’ saved [1023114636/1023114636]

    mspuser:~/environment $ 
    ```

2. Unzip downloaded file

    ```console
    unzip aws-schema-conversion-tool-1.0.latest.zip
    ```

    ```console
    mspuser:~/environment $ unzip aws-schema-conversion-tool-1.0.latest.zip 
    Archive:  aws-schema-conversion-tool-1.0.latest.zip
    inflating: aws-schema-conversion-tool-1.0.665.deb  
    inflating: agents/aws-cassandra-extractor-1.0.665-1.x86_64.rpm  
    inflating: agents/aws-cassandra-extractor-1.0.665.deb  
    inflating: agents/aws-schema-conversion-tool-extractor-2.0.1.665-1.x86_64.rpm  
    inflating: agents/aws-schema-conversion-tool-extractor-2.0.1.665.deb  
    inflating: agents/aws-schema-conversion-tool-extractor-2.0.1.665.msi  
    inflating: dmsagent/aws-schema-conversion-tool-dms-agent-3.4.5-R2.x86_64.rpm  
    mspuser:~/environment $ ls -la
    total 1489664
    drwxr-xr-x  5 ubuntu ubuntu       4096 Sep  7 02:02 .
    drwxr-xr-x 13 ubuntu ubuntu       4096 Sep  7 01:48 ..
    drwxrwxr-x  5 ubuntu ubuntu       4096 Aug 29 09:00 .c9
    drwxrwxr-x  2 ubuntu ubuntu       4096 Sep  7 02:02 agents
    -rw-r--r--  1 ubuntu ubuntu  502254846 Aug 19 16:23 aws-schema-conversion-tool-1.0.665.deb
    -rw-rw-r--  1 ubuntu ubuntu 1023114636 Aug 29 15:50 aws-schema-conversion-tool-1.0.latest.zip
    drwxrwxr-x  2 ubuntu ubuntu       4096 Sep  7 02:02 dmsagent
    mspuser:~/environment $ 
    ```

3. Run AWS SCT installer file extracted

    ***Change ```<build-number>``` according to your current build number***

    ```console
    sudo dpkg -i aws-schema-conversion-tool-1.0.<build-number>.deb
    ```

    ```console
    mspuser:~/environment $ sudo dpkg -i aws-schema-conversion-tool-1.0.665.deb
    (Reading database ... 106543 files and directories currently installed.)
      ⋮
    Adding shortcut to the menu
    mspuser:~/environment $ 
    ```

### 5. Download and Install JDBC drivers for AWS SCT

Refer to [Downloading the required database drivers](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Installing.html#CHAP_Installing.JDBCDrivers)

1. Downloading the required database drivers

    ```console
    wget https://download.oracle.com/otn-pub/otn_software/jdbc/217/ojdbc8.jar
    wget https://jdbc.postgresql.org/download/postgresql-42.2.19.jar
    wget https://downloads.mariadb.com/Connectors/java/connector-java-2.4.1/mariadb-java-client-2.4.1.jar
    ```

    ```console
    mspuser:~/environment $ wget https://download.oracle.com/otn-pub/otn_software/jdbc/217/ojdbc8.jar
    --2022-09-07 08:38:18--  https://download.oracle.com/otn-pub/otn_software/jdbc/217/ojdbc8.jar
      ⋮
    2022-09-07 08:38:19 (85.0 MB/s) - ‘ojdbc8.jar’ saved [5089412/5089412]
    mspuser:~/environment $ wget https://jdbc.postgresql.org/download/postgresql-42.2.19.jar
    --2022-09-07 08:40:18--  https://jdbc.postgresql.org/download/postgresql-42.2.19.jar
      ⋮
    2022-09-07 08:40:20 (1.09 MB/s) - ‘postgresql-42.2.19.jar’ saved [1005078/1005078]
    mspuser:~/environment $ wget https://downloads.mariadb.com/Connectors/java/connector-java-2.4.1/mariadb-java-client-2.4.1.jar
    --2022-09-07 08:40:49--  https://downloads.mariadb.com/Connectors/java/connector-java-2.4.1/mariadb-java-client-2.4.1.jar
      ⋮
    2022-09-07 08:40:50 (698 KB/s) - ‘mariadb-java-client-2.4.1.jar’ saved
    mspuser:~/environment $ 
    ```

2. Installing JDBC drivers

    Create a directory to store the JDBC drivers in

    ```console
    sudo mkdir –p /usr/local/jdbc-drivers
    ```

    ```console
    mspuser:~/environment $ sudo mkdir –p /usr/local/jdbc-drivers
    mspuser:~/environment $
    ```

    Install the JDBC driver for Oracle database engine

    ```console
    
    ```

    ```console
    
    ```

    Install the JDBC driver for PostgreSQL database engine

    ```console
    
    ```

    ```console
    
    ```

    Install the JDBC driver for MariaDB database engine

    ```console
    
    ```

    ```console
    
    ```

## 2. AWS

1. Get an AWS CloudFormation stack template body

    ```bash
    wget https://github.com/t2yijaeho/AWS-SCT-and-DMS/raw/matia/Template/DMS-Oracle2Postgre.yaml
    ```

2. Get your local machine public IP address in the browser

    [Your public IP address](http://checkip.amazonaws.com/)

3. Create an AWS CloudFormation stack

    ***Change `<My Custom Image ID>` to your Custom Amazon Machine Image ID***
    
    ***Change `<My IP>` to your local machine IP address (ParameterValue must be in CIDR notation)***

    ```bash
    aws cloudformation create-stack \
      --stack-name Ora2PgDMS \
      --template-body file://./DMS-Oracle2Postgre.yaml \
      --parameters ParameterKey=CustomImageID,ParameterValue="<My Custom Image ID>" \
      ParameterKey=LocalLocation,ParameterValue="<My IP>/32"
    ```

4. AWS CloudFormation returns following output

    ```json
    {
    "StackId": "arn:aws:cloudformation:us-abcd-x:123456789012:stack/MigrationVM/b4d0f5e0-d4c2-11ec-9529-06edcc65f112"
    }
    ```

5. Monitor the progress by the stack's events in AWS management console
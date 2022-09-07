# AWS Schema Conversion Tool and Database Migration Service

## 1. Prepare Environment

### 1. Prepare source and target database
    Refer to [Launch Custom AMI EC2](https://github.com/t2yijaeho/Custom-AMI-EC2-with-CloudFormation)
    Refer to [Create RDS PostgreSQL](https://github.com/t2yijaeho/Amazon-RDS-PostgreSQL-with-AWS-CloudFormation)

### 2. Create Windows firewall inbound rule for Oracle TNS port(1521) using PowerShell script

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

### 3. Install AWS Schema Conversion Tool

## 2. AWS

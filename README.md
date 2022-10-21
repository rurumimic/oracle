# Oracle

- oracle/[docker-images](https://github.com/oracle/docker-images)
- Sample
   - [Sample Schema Diagrams](https://docs.oracle.com/en/database/oracle/oracle-database/21/comsc/schema-diagrams.html#GUID-D268A4DE-BA8D-428E-B47F-80519DC6EE6E) 
     - [Diagrams](diagrams/README.md)
     - [DB examples](https://github.com/oracle-samples/oracle-db-examples)
     - [DB datasets](https://github.com/oracle-samples/db-sample-schemas)

## Database

### Download Starterset Dataset

```bash
mkdir -p docker/share
git clone https://github.com/oracle/db-sample-schemas.git docker/share/schemas
```

Change all embedded paths to match your working directory:

```bash
cd docker/share/schemas
workdir=/share/schemas
perl -p -i.bak -e 's#__SUB__CWD__#'$workdir'#g' *.sql */*.sql */*.dat
```

### Start Docker

- [Single Instance](https://github.com/oracle/docker-images/tree/main/OracleDatabase/SingleInstance)
- [database-18.4.0-xe.yml](docker/database-18.4.0-xe.yml)

```bash
docker compose -f docker/database-18.4.0-xe.yml up -d
docker compose -f docker/database-18.4.0-xe.yml down # -v: Remove Volumes
```

### Attach to the Container

```bash
docker compose -f docker/database-18.4.0-xe.yml exec -it oracle bash
```

Result: [log/compose.up.log](log/compose.up.log)

### SQL*Plus

- download: [Instant Client](https://www.oracle.com/cis/database/technologies/instant-client/downloads.html)
- docker: 
  - oracle/docker-images/[OracleInstantClient](https://github.com/oracle/docker-images/tree/main/OracleInstantClient)
  - [oraclelinux8-instantclient](https://github.com/oracle/docker-images/pkgs/container/oraclelinux8-instantclient)


```bash
sqlplus <USER>/<PASSWORD>@//localhost:1521/<ORACLE_SID> [as sysdba]
```

#### Login

```bash
bash-4.2# sqlplus sys/oracle@//localhost:1521/XE as sysdba

SQL*Plus: Release 18.0.0.0.0 - Production on Fri Oct 21 09:34:55 2022
Version 18.4.0.0.0quit

Copyright (c) 1982, 2018, Oracle.  All rights reserved.


Connected to:
Oracle Database 18c Express Edition Release 18.0.0.0.0 - Production
Version 18.4.0.0.0

SQL>
```

```bash
sqlplus sys/oracle@//localhost:1521/XE as sysdba
sqlplus system/oracle@//localhost:1521/XE
sqlplus pdbadmin/oracle@//localhost:1521/XEPDB1
```

Sample SQL>:

```SQL
SELECT USERNAME FROM ALL_USERS ORDER BY USERNAME;
SELECT TABLESPACE_NAME FROM USER_TABLESPACES;
```

### Insert data

Oracle Database Sample Schemas: [2.5. Run the installation script](https://github.com/oracle-samples/db-sample-schemas#25--run-the-installation-script)

```bash
cd /share/schemas

# sqlplus system/systempw@connect_string
sqlplus system/oracle@//localhost:1521/XE

# @mksample systempw syspw hrpw oepw pmpw ixpw shpw bipw users temp /your/path/to/log/ connect_string
@mksample oracle oracle hrpw oepw pmpw ixpw shpw bipw users temp /var/log/my-log/ XEPDB1
# log directory: Use an absolute path and also append a trailing slash to the log directory name.
```

Result: [log/schemas.log](log/schemas.log)

#### Check Data

Increase [LINSIZE](https://www.oreilly.com/library/view/oracle-sqlplus-the/0596007469/re69.html): default 80. (1 ~ 32767)

```sql
SET LINESIZE 500
SET WRAP OFF
```

##### Users

```sql
-- sqlplus pdbadmin/oracle@//localhost:1521/XEPDB1
SELECT USERNAME FROM ALL_USERS WHERE USERNAME IN ('HR', 'OE', 'PM', 'IX', 'SH', 'BI') ORDER BY USERNAME;
-- 6 rows selected.
```

##### HR

```sql
-- sqlplus hr/hrpw@//localhost:1521/XEPDB1
SELECT * FROM EMPLOYEES WHERE SALARY < 2300;
```

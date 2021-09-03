---
description: 'Ubuntu 20.04 LTS, Postgrsql 12'
---

# Install postgresql

### 삭제 

```bash
psql --version #버전 확인

systemctl stop postgresql-12
systemctl disable postgresql-12 

rm -rf /var/lib/postgresql
userdel postgres

sudo apt-get --purge remove postgresql\*
dpkg -l | grep postgres
```

### 재설치

```bash
sudo apt update

# Create the file repository configuration:
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
# Import the repository signing key:
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
# Update the package lists:
sudo apt-get update

# Install the latest version of PostgreSQL.
# If you want a specific version, use 'postgresql-12' or similar instead of 'postgresql':
sudo apt-get -y install postgresql-12
```

```bash
sudo apt update

sudo apt -y install vim bash-completion wget

wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list

sudo apt update

sudo apt -y install postgresql-12 postgresql-client-12

systemctl status postgresql.service

systemctl status postgresql@12-main.service

sudo su - postgres

psql -c "alter user postgres with password '1234'"
```

{% tabs %}
{% tab title="파일 생성" %}
```bash
sudo nano /etc/apt/sources.list.d/pgdg.list


```
{% endtab %}

{% tab title="pgdg.list" %}
```
deb http://apt.postgresql.org/pub/repos/apt/ focal-pgdg main
```
{% endtab %}
{% endtabs %}

```bash
sudo su - postgres
# postgres 비밀번호 설정 
psql -c "alter user postgres with password 'password'"

~postgres# createuser --pwprompt --interactive [userid]


```

### 외부접속

{% tabs %}
{% tab title="terminal" %}
```bash
netstat -ntlp

# 5432 port 있는 것 확인

sudo vi /etc/postgresql/12/main/pg_hba.conf

sudo vi /etc/postgresql/12/main/postgresql.conf
```
{% endtab %}

{% tab title="pg\_hba.conf" %}
```
host     all    all    0.0.0.0/0    md5 #추가
local    all    all                 md5 #peer -> md5 수정 
```
{% endtab %}

{% tab title="postgresql.conf" %}
```
listen_address = '*' # 수정 
```
{% endtab %}
{% endtabs %}

```bash
service postgresql restart
```

## Install DBeaver

```bash
# ATLAS 설치 시 jdk 8
sudo apt -y install openjdk-8-jdk openjdk-8-jre

java -version
```

```bash
wget -O - https://dbeaver.io/debs/dbeaver.gpg.key | sudo apt-key add -
echo "deb https://dbeaver.io/debs/dbeaver-ce /" | sudo tee /etc/apt/sources.list.d/dbeaver.list

sudo apt update
sudo apt -y install dbeaver-ce

apt policy dbeaver-ce
```

## Install pgadmin3 X

```bash
curl https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo apt-key add
sudo sh -c 'echo "deb https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list && apt update'

sudo apt install pgadmin4
```



{% embed url="https://github.com/OHDSI/WebAPI/wiki/PostgreSQL-Installation-Guide" %}



## 윈도우의 경우 

postgresql12 다운로드  [https://dog-developers.tistory.com/122](https://dog-developers.tistory.com/122)

pgadmin4 혹은 dbeaver를 다운받아 작업한다'



## CDM 데이터베이스 구조 

{% embed url="https://github.com/OHDSI/WebAPI/wiki/PostgreSQL-Installation-Guide" %}

참고하여 그대로 실행 

```sql
-- create role
CREATE ROLE ohdsi_admin
  CREATEDB REPLICATION
   VALID UNTIL 'infinity';
COMMENT ON ROLE ohdsi_admin
  IS 'Administration group for OHDSI applications';
  
  

CREATE ROLE ohdsi_app
   VALID UNTIL 'infinity';
COMMENT ON ROLE ohdsi_app
  IS 'Application groupfor OHDSI applications';
  


CREATE ROLE ohdsi_admin_user LOGIN ENCRYPTED PASSWORD 'admin1234'
   VALID UNTIL 'infinity';
GRANT ohdsi_admin TO ohdsi_admin_user;
COMMENT ON ROLE ohdsi_admin_user
  IS 'Admin user account for OHDSI applications';
  
  
CREATE ROLE ohdsi_app_user LOGIN ENCRYPTED PASSWORD 'admin1234'
   VALID UNTIL 'infinity';
GRANT ohdsi_app TO ohdsi_app_user;
COMMENT ON ROLE ohdsi_app_user
  IS 'Application user account for OHDSI applications';
  
  
-- create database 
CREATE DATABASE "OHDSI"
WITH ENCODING='UTF8'
     OWNER=ohdsi_admin
     CONNECTION LIMIT=-1;
COMMENT ON DATABASE "OHDSI"
  IS 'OHDSI database';
GRANT ALL ON DATABASE "OHDSI" TO GROUP ohdsi_admin;
GRANT CONNECT, TEMPORARY ON DATABASE "OHDSI" TO GROUP ohdsi_app;


-- connect OHDSI

CREATE SCHEMA cdm
       AUTHORIZATION ohdsi_admin;
COMMENT ON SCHEMA cdm
  IS 'Schema containing tables to support WebAPI functionality';
GRANT USAGE ON SCHEMA cdm TO PUBLIC;
GRANT ALL ON SCHEMA cdm TO GROUP ohdsi_admin;
GRANT USAGE ON SCHEMA cdm TO GROUP ohdsi_app;


CREATE SCHEMA webapi
       AUTHORIZATION ohdsi_admin;
COMMENT ON SCHEMA webapi
  IS 'Schema containing tables to support WebAPI functionality';
GRANT USAGE ON SCHEMA webapi TO PUBLIC;
GRANT ALL ON SCHEMA webapi TO GROUP ohdsi_admin;
GRANT USAGE ON SCHEMA webapi TO GROUP ohdsi_app;



ALTER DEFAULT PRIVILEGES IN SCHEMA webapi
    GRANT INSERT, SELECT, UPDATE, DELETE, REFERENCES, TRIGGER ON TABLES
    TO ohdsi_app;

ALTER DEFAULT PRIVILEGES IN SCHEMA webapi
    GRANT SELECT, USAGE ON SEQUENCES
    TO ohdsi_app;

ALTER DEFAULT PRIVILEGES IN SCHEMA webapi
    GRANT EXECUTE ON FUNCTIONS
    TO ohdsi_app;

ALTER DEFAULT PRIVILEGES IN SCHEMA webapi
    GRANT USAGE ON TYPES
    TO ohdsi_app;
```

1. OHDSI 데이터베이스  
2. cdm, webapi 스키마 추가




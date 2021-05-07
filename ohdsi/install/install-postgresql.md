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

## Install pgadmin3

```bash
curl https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo apt-key add
sudo sh -c 'echo "deb https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list && apt update'

sudo apt install pgadmin4
```



[https://github.com/OHDSI/WebAPI/wiki/PostgreSQL-Installation-Guide](https://github.com/OHDSI/WebAPI/wiki/PostgreSQL-Installation-Guide)


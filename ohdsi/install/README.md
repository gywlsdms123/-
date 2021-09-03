# Install

## Install Tomcat 9 

Becoming a super hero is a fairly straight forward process:

```
$ sudo apt update
$ sudo apt-cache search tomcat
$ sudo apt install tomcat9 tomcat9-admin
$ ss -ltn

$ sudo systemctl enable tomcat9
$ sudo ufw allow from any to any port 8080 proto tcp
```

{% hint style="info" %}
 ubuntu 20.04 LTS / Tomcat 9 / jdk 8 
{% endhint %}

Create User

{% code title="$ sudo nano /etc/tomcat9/tomcat-users.xml" %}
```bash
 <role rolename="manager-gui"/>
 <user username="tomcat" password="s3cret" roles="manager-gui"/>
```
{% endcode %}

{% code title="$ sudo nano /usr/share/tomcat9-admin/manager/WEB-INF/web.xml" %}
```bash
<multipart-config>
  <!-- 250MB max -->
  <max-file-size>252428800</max-file-size>
  <max-request-size>252428800</max-request-size>
  <file-size-threshold>0</file-size-threshold>
</multipart-config>
```
{% endcode %}

```bash
$ sudo systemctl restart tomcat9
```

## Install JDK 8

```bash
$ sudo apt install openjdk-8-jdk

$ javaj -version
openjdk version "1.8.0_292"
OpenJDK Runtime Environment (build 1.8.0_292-8u292-b10-0ubuntu1~20.04-b10)
OpenJDK 64-Bit Server VM (build 25.292-b10, mixed mode)

```

{% tabs %}
{% tab title="env 1" %}
```bash
$ sudo nano /etc/environment


JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"

source /etc/environmnet
echo $JAVA_HOME

```
{% endtab %}

{% tab title="env 2" %}
```

```
{% endtab %}
{% endtabs %}

## Install Maven

```bash
apt-get update -y
apt-get upgrade -y

cd /opt
wget https://downloads.apache.org/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz

tar xzf apache-maven-3.6.3-bin.tar.gz
mv apache-maven-3.6.3 apachemaven
```

#### Setup Environment Variable

```bash
sudo nano /etc/profile.d/maven.sh

export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64
export M2_HOME=/opt/maven
export MAVEN_HOME=/opt/maven
export PATH=${M2_HOME}/bin:${PATH}
```

```bash
chmod +x /etc/profile.d/maven.sh
source /etc/profile.d/maven.sh

mvn -version
```

#### other method

```bash
$ sudo apt update
$ sudo apt install maven

mvn -version
```

## 환경변수 설정

{% tabs %}
{% tab title=".bashrc" %}
```bash
sudo nano ~/.bashrc

export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$PATH:$JAVA_HOME/bin
export MVN_HOME=/usr/share/maven
export PATH=$PATH:$MVN_HOME/bin
export CATALINA_HOME=/var/lib/tomcat9
export PATH=$PATH:$CATALINA_HOME/bin

source ~/.bashrc
```
{% endtab %}

{% tab title="profile" %}
```
sudo nano /etc/profile

export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$PATH:$JAVA_HOME/bin
export MVN_HOME=/usr/share/maven
export PATH=$PATH:$MVN_HOME/bin
export CATALINA_HOME=/var/lib/tomcat9
export PATH=$PATH:$CATALINA_HOME/bin

export CLASSPATH=.:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar:$CATALINA_HOME/>

source /etc/profile
```
{% endtab %}
{% endtabs %}



윈도우 컴퓨터의 경우

JDK8 [https://the-duchi.tistory.com/4](https://the-duchi.tistory.com/4)

maven [https://jiwondh.github.io/2018/08/08/maven-install/](https://jiwondh.github.io/2018/08/08/maven-install/)

tomcat [https://forkmk.tistory.com/217](https://forkmk.tistory.com/217) 

차례로 다운


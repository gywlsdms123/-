---
description: for atlas
---

# Install WebAPI

## PostgreSQL Setup Guide 

* CDM으로 구축한 ohdsi\_cdm 말고 다른 데이터베이스를 생성해야 함

{% embed url="https://github.com/OHDSI/WebAPI" %}



```text
git checkout refs/tags/v2.9.0
```

{% tabs %}
{% tab title="setting.xml" %}
```text
<profile>
    <id>webapi-postgresql</id>
    <properties>
      <datasource.driverClassName>org.postgresql.Driver</datasource.driverClassName>
      <datasource.url>jdbc:postgresql://192.168.10.10:5432/OHDSI</datasource.url>
      <datasource.username>ohdsi_app_user</datasource.username>
      <datasource.password>admin1234</datasource.password>
      <datasource.dialect>postgresql</datasource.dialect>
      <datasource.ohdsi.schema>webapi</datasource.ohdsi.schema>
      <flyway.datasource.driverClassName>${datasource.driverClassName}</flyway.datasource.driverClassName>
      <flyway.datasource.url>${datasource.url}</flyway.datasource.url>
      <flyway.datasource.username>ohdsi_admin_user</flyway.datasource.username>
      <flyway.datasource.password>admin1234</flyway.datasource.password>
      <flyway.locations>classpath:db/migration/postgresql</flyway.locations>
      <security.provider>DisabledSecurity</security.provider>
      <security.token.expiration>43200</security.token.expiration>
      <security.origin>*</security.origin>
      <security.ssl.enabled>false</security.ssl.enabled>
      <security.cors.enabled>false</security.cors.enabled>
      <security.oauth.callback.ui>http://localhost:8080/Atlas/#/welcome</security.oauth.callback.ui>
      <security.oauth.callback.api>http://localhost:8080/WebAPI/user/oauth/callback</security.oauth.callback.api>
      <security.oauth.google.apiKey></security.oauth.google.apiKey>
      <security.oauth.google.apiSecret></security.oauth.google.apiSecret>
      <security.oauth.facebook.apiKey></security.oauth.facebook.apiKey>
      <security.oauth.facebook.apiSecret></security.oauth.facebook.apiSecret>
      <security.oauth.github.apiKey></security.oauth.github.apiKey>
      <security.oauth.github.apiSecret></security.oauth.github.apiSecret>
      <security.oid.clientId></security.oid.clientId>
      <security.oid.apiSecret></security.oid.apiSecret>
      <security.oid.url></security.oid.url>
      <security.oid.redirectUrl>http://localhost/index.html#/welcome/</security.oid.redirectUrl>
      <security.ldap.dn>cn={0},dc=example,dc=org</security.ldap.dn>
      <security.ldap.url>ldap://localhost:389</security.ldap.url>
      <security.ldap.baseDn></security.ldap.baseDn>
      <security.ldap.system.username></security.ldap.system.username>
      <security.ldap.system.password></security.ldap.system.password>
      <security.ad.url>ldap://localhost:389</security.ad.url>
      <security.ad.searchBase>CN=Users,DC=example,DC=org</security.ad.searchBase>
      <security.ad.principalSuffix>@example.org</security.ad.principalSuffix>
      <security.ad.system.username></security.ad.system.username>
      <security.ad.system.password></security.ad.system.password>
      <security.ad.searchFilter></security.ad.searchFilter>
      <security.ad.ignore.partial.result.exception>true</security.ad.ignore.partial.result.exception>
      <security.ad.result.count.limit>30000</security.ad.result.count.limit> <!-- 0 means no limit -->
      <security.ad.default.import.group>public</security.ad.default.import.group>
      <security.cas.loginUrl></security.cas.loginUrl>
      <security.cas.callbackUrl></security.cas.callbackUrl>
      <security.cas.serverUrl></security.cas.serverUrl>
      <security.cas.cassvcs></security.cas.cassvcs>
      <security.cas.casticket>casticket</security.cas.casticket>
      <security.googleIap.cloudProjectId></security.googleIap.cloudProjectId>
      <security.googleIap.backendServiceId></security.googleIap.backendServiceId>
      <security.maxLoginAttempts>3</security.maxLoginAttempts>
      <security.duration.initial>10</security.duration.initial>
      <security.duration.increment>10</security.duration.increment>
      <security.db.datasource.schema>${datasource.ohdsi.schema}</security.db.datasource.schema>
      <security.db.datasource.url>${datasource.url}</security.db.datasource.url>
      <security.db.datasource.driverClassName>${datasource.driverClassName}</security.db.datasource.driverClassName>
      <security.db.datasource.username>${datasource.username}</security.db.datasource.username>
      <security.db.datasource.password>${datasource.password}</security.db.datasource.password>
      <security.db.datasource.authenticationQuery>select password from ${security.db.datasource.schema}.users where lower(email) = lower(?)</security.db.datasource.authenticationQuery>
    </properties> 
  </profile>     
```
{% endtab %}

{% tab title="pom.xml" %}
```
<plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>2.18.1</version>
        <configuration>
          <skipTests>${skipUnitTests}</skipTests>
          <testFailureIgnore>true</testFailureIgnore> #추
        </configuration>
      </plugin>
```
{% endtab %}
{% endtabs %}

```text
mvn clean package -DskipTests -s WebAPIConfig/settings.xml -P webapi-postgresql

```

ohdsi\_app_\__user 모든 테이블에 권한 insert, select, update, delete 부여 

localhost:8080 접속 war file deploy 




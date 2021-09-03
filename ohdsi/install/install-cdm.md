---
description: OHDSI OMOP CDM
---

# Install CDM

## Install postgresql 12 

[link](https://app.gitbook.com/@gywlsdms123/s/notes/~/drafts/-M_3aShBp1b9ocuG715S/ohdsi/install/install-postgresql)

## OHDSI Software 설치 순서 

1. **CDM** \([CommonDataModel V5.3.1](https://github.com/OHDSI/CommonDataModel/tree/v5.3.1_fixes)\)
2. **ATLAS**: web-based tool \([link](https://github.com/OHDSI/Atlas)\) \([OHDSI WebAPI](https://github.com/OHDSI/WebAPI)\)
3. **ACHILLES** \([link](https://github.com/OHDSI/Achilles)\)

## 1. Download CDM data 

#### CDM data \(csv\)

* [CDM DATA - synpuf5](https://drive.google.com/file/d/18EjMxyA6NsqBo9eed_Gab1ESHWPxJygz/view)
* CDM DATA - voca

#### CDM database

```bash
git clone https://github.com/OHDSI/CommonDataModel.git

cd CommonDataModel
git checkout v5.3.1 
```

#### CommonDataModel/Postgresql/README.md

{% tabs %}
{% tab title="README.md" %}
```text
Common-Data-Model / PostgreSQL
=================
This folder contains the SQL scripts for PostgreSQL.
In order to create your instantiation of the Common Data Model, we recommend following these steps:
1. Create an empty schema.
2. Execute the script `OMOP CDM ddl - PostgreSQL.sql` to create the tables and fields.
3. Load your data into the schema.
4. Execute the script `OMOP CDM constraints - PostgreSQL.sql` to add the constraints (primary and foreign keys).
5. Execute the script `OMOP CDM indexes required - PostgreSQL.sql` to add the minimum set of indexes we recommend.
Note: you could also apply the constraints and/or the indexes before loading the data, but this will slow down the insertion of the data considerably.
```
{% endtab %}
{% endtabs %}

## 2. Insert CDM data

#### postgresql 

1. ohdsi\_cdm 데이터베이스 생성
2. synpuf5 스키마 생성 - 터미널에서 CREATE SCHMA synpuf5; 또는 DBeaver로 생성 \(**1. Create an empty schema**

```bash
psql -h localhost -U postgres -d ohdsi

postgres 사용자의 암호: 
psql (12.6 (Ubuntu 12.6-1.pgdg20.04+1))
SSL 연결정보 (프로토콜: TLSv1.3, 암호화기법: TLS_AES_256_GCM_SHA384, 비트: 256, 압축: off)
도움말을 보려면 "help"를 입력하십시오.

ohdsi=# CREATE SCHEMA synpuf5;
CREATE SCHEMA

```

![SET search\_path to synpuf5; &#xCD94;&#xAC00;](../../.gitbook/assets/image%20%288%29.png)

#### 테이블 생성 스크립트 실행

#### \(2. Execute the script `OMOP CDM ddl - PostgreSQL.sql` to create the tables and fields.\)

```bash
cd PostgreSQL

~/CommonDataModel/PostgreSQL$ psql -h localhost -U postgres -d ohdsi_cdm -a -f OMOP\ CDM\ ddl\ -\ PostgreSQL.sql
```

#### 3. Loda your data into the schema

* [CDM DATA - synpuf5](https://drive.google.com/file/d/18EjMxyA6NsqBo9eed_Gab1ESHWPxJygz/view)
* CDM DATA - voca 는 아테나에서 다운 

폴더 지정 후 다운로드 

{% tabs %}
{% tab title="insert.sql" %}
```bash
SET search_path to synpuf5;

\COPY provider FROM '~/synpuf5pct_20180710/provider.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';
\COPY observation_period FROM '~/synpuf5pct_20180710/observation_period.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';
\COPY care_site FROM '~/synpuf5pct_20180710/care_site.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';
\COPY cost FROM '~/synpuf5pct_20180710/cost.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';
\COPY death FROM '~/synpuf5pct_20180710/death.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';
\COPY device_exposure FROM '~/synpuf5pct_20180710/device_exposure.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';
\COPY location FROM '~/synpuf5pct_20180710/location.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';
\COPY measurement FROM '~/synpuf5pct_20180710/measurement.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';
\COPY condition_occurrence FROM '~/synpuf5pct_20180710/condition_occurrence.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';
\COPY drug_era FROM '~/synpuf5pct_20180710/drug_era.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';
\COPY cdm_source FROM '~/synpuf5pct_20180710/cdm_source.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';
\COPY visit_occurrence FROM '~/synpuf5pct_20180710/visit_occurrence.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';
\COPY person FROM '~/synpuf5pct_20180710/person.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';
\COPY observation FROM '~/synpuf5pct_20180710/observation.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';
\COPY payer_plan_period FROM '~/synpuf5pct_20180710/payer_plan_period.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';
\COPY procedure_occurrence FROM '~/synpuf5pct_20180710/procedure_occurrence.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';
\COPY condition_era FROM '~/synpuf5pct_20180710/condition_era.csv' WITH DELIMITER E'\t' CSV QUOTE E'\b';


```
{% endtab %}
{% endtabs %}

postgresql 폴더에 sql 파일 생성 - 그대로 복사 \(대소문자 구분하니 파일명 주의\)

```bash
~/CommonDataModel/PostgreSQL$ psql -h localhost -U postgres -d ohdsi_cdm -a -f insertData.sql
```

VocabImport 폴더 -&gt; voca path 수정 후 위와 같이 insert

```bash
psql -h localhost -U postgres -d ohdsi_cdm -a -f OMOP\ CDM\ vocabulary\ load\ -\ PostgreSQL.sql
```

#### **4**~~**. Execute the script `OMOP CDM constraints - PostgreSQL.sql` to add the constraints \(primary and foreign keys\).**~~

```bash
psql -h localhost -U postgres -d ohdsi_cdm -a -f OMOP\ CDM\ constraints\ -\ PostgreSQL.sql
```

#### **5. Execute the script `OMOP CDM indexes required - PostgreSQL.sql` to add the minimum set of indexes we recommend.**

```bash
psql -h localhost -U postgres -d ohdsi_cdm -a -f OMOP\ CDM\ indexes required\ -\ PostgreSQL.sql
```

{% embed url="https://github.com/OHDSI/WebAPI/wiki/CDM-Configuration" %}

WebAPI 문서 무조건 확인할 것 

1. CDM data 적재 
2. Voca 적재 
3. 인덱스 및 constraints 설정

1, 2, 3번 완료 시 데이터 준비는 끝


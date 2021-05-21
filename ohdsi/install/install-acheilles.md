# Install Acheilles

## Download R & R Studio

```
$ sudo apt install dirmngr gnupg apt-transport-https ca-certificates software-properties-common

$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9
$ sudo add-apt-repository 'deb https://cloud.r-project.org/bin/linux/ubuntu focal-cran40/'

$ sudo apt install r-base

$ R --version
```

```bash
$ sudo apt install build-essential

$ R
> install.packages("stringr")
```

#### R stud

```text
$ sudo apt update
$ sudo apt -y install r-base gdebi-core

$ ls
rstudio-1.2.5019-amd64.deb

$ sudo gdebi rstudio-1.2.5019-amd64.deb
$ rstudio
```

## Ubuntu 18.04 LTS version

```text
$ sudo apt-get install r-base

$ R --version

$ sudo apt-get install gdebi-core
$ wget https://download2.rstudio.org/rstudio-server-1.1.456-amd64.deb
$ sudo gdebi rstudio-server-1.1.456-amd64.deb

$ rstudio-server status

$ sudo rstudio-server verify-installation
$ sudo ufw allow 8787
```

{% hint style="info" %}
http://&lt;server-ip&gt;:8787
{% endhint %}

## Achilles 설치 

#### [https://ohdsi.github.io/Achilles/](https://ohdsi.github.io/Achilles/)

```text
if (!require("devtools")) install.packages("devtools")

# To install the master branch
devtools::install_github("OHDSI/Achilles")

# To install latest release (if master branch contains a bug for you)
# devtools::install_github("OHDSI/Achilles@*release")  

# To avoid Java 32 vs 64 issues 
# devtools::install_github("OHDSI/Achilles", args="--no-multiarch")  
```

{% tabs %}
{% tab title="R connect" %}
```r
library(Achilles)
connectionDetails <- createConnectionDetails(
  dbms="postgresql", 
  server="localhost/OHDSI", 
  user="secret", 
  password='secret', 
  port="5439",
  path="<jdbc-path>")
```
{% endtab %}

{% tab title="Single-thread" %}
```r
achilles(connectionDetails, 
  cdmDatabaseSchema = "cdm5_inst", 
  resultsDatabaseSchema="results",
  vocabDatabaseSchema = "vocab",
  numThreads = 1,
  sourceName = "My Source Name", 
  cdmVersion = "5.3.0",
  runHeel = TRUE,
  runCostAnalysis = TRUE)
```
{% endtab %}

{% tab title="Multi-thread" %}
```r
achilles(connectionDetails, 
  cdmDatabaseSchema = "cdm5_inst", 
  resultsDatabaseSchema = "results",
  scratchDatabaseSchema = "scratch",
  vocabDatabaseSchema = "vocab",
  numThreads = 10,
  sourceName = "My Source Name", 
  cdmVersion = "5.3.0",
  runHeel = TRUE,
  runCostAnalysis = TRUE)
```
{% endtab %}

{% tab title="A" %}
```r
?createConnectionDetails
```
{% endtab %}
{% endtabs %}

#### [https://github.com/OHDSI/AchillesWeb.git](https://github.com/OHDSI/AchillesWeb.git)

{% tabs %}
{% tab title="AchillesWeb" %}
```text
git clone https://github.com/OHDSI/AchillesWeb.git

cd AchillesWeb
mkdir data
cd data
mkdir ORGANISATION

sudo nano datasources.json

```
{% endtab %}

{% tab title="datasources.json" %}
{% code title="datasources.json " %}
```javascript
 { "datasources":[ { "name":"sample CDMv5", "folder":"ORGANISATION", "cdmVersion": 5 } ] } 

```
{% endcode %}
{% endtab %}
{% endtabs %}

```r
exportToJson(connectionDetails, 
  cdmDatabaseSchema = "cdm5_inst", 
  resultsDatabaseSchema = "results", 
  outputPath = "~/문서/OHDSI/AchillesWeb/data/ORGANISATION", 
  compressIntoOneFile = TRUE) # creates gzipped file of all JSON files
```

```r
achillesHeel(connectionDetails, 
  cdmDatabaseSchema = "cdm5_inst", 
  resultsDatabaseSchema = "results", 
  scratchDatabaseSchema = "scratch",
  numThreads = 10, # multi-threaded mode
  cdmVersion = "5.3.0")
```

```r
achilles(connectionDetails, 
  cdmDatabaseSchema = "cdm5_inst", 
  resultsDatabaseSchema="results",
  vocabDatabaseSchema = "vocab",
  numThreads = 1,
  sourceName = "My Source Name", 
  cdmVersion = "5.3.0",
  runHeel = TRUE,
  optimizeAtlasCache = TRUE) # creates additional table to store results for Atlas cache
```

```r
optimizeAtlasCache(connectionDetails,
                   resultsDatabaseSchema="results",
                   vocabDatabaseSchema = "dbo")   
```

```r
INSERT INTO "webapi"."source" (
    "source_id",
    "source_name",
    "source_key",
    "source_connection",
    "source_dialect",
    "username",
    "password",
    "krb_auth_method",
    "keytab_name",
    "krb_keytab",
    "krb_admin_server",
    "deleted_date",
    "created_by_id",
    "created_date",
    "modified_by_id",
    "modified_date" 
)
VALUES
    (
        1,
        'OHDSI V5 Database',
        'OHDSI-CDMV5',
        'jdbc:postgresql://localhost/ohdsi',
        'postgresql',
        'user',
        'password',
        'PASSWORD',
        NULL,
        NULL,
        NULL,
        NULL,
        NULL,
        NULL,
        NULL,
        NULL 
    );
INSERT INTO "webapi"."source_daimon"("source_daimon_id", "source_id", "daimon_type", "table_qualifier", "priority") VALUES (1, 1, 0, 'cdm', 2);
INSERT INTO "webapi"."source_daimon"("source_daimon_id", "source_id", "daimon_type", "table_qualifier", "priority") VALUES (2, 1, 1, 'cdm', 2);
INSERT INTO "webapi"."source_daimon"("source_daimon_id", "source_id", "daimon_type", "table_qualifier", "priority") VALUES (3, 1, 2, 'results', 2);
INSERT INTO "webapi"."source_daimon"("source_daimon_id", "source_id", "daimon_type", "table_qualifier", "priority") VALUES (4, 1, 5, 'temp', 2);
```


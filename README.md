# datalake-on-premise


### INSTALL s3cmd and java jdk

```bash
sudo apt update
sudo apt install -y \
    s3cmd \
    openjdk-11-jre-headless 
```

### INSTALL TRINO CLI (terminal)
```bash
# wget https://repo1.maven.org/maven2/io/trino/trino-cli/352/trino-cli-351-executable.jar \
wget https://repo1.maven.org/maven2/io/trino/trino-cli/446/trino-cli-446-executable.jar -O trino
chmod +x trino  
```


```bash
s3cmd --config minio.s3cfg --configure
```

Use the following configuration for the `s3cmd` configuration when prompted:

```bash
Access Key: minio_access_key
Secret Key: minio_secret_key
Default Region [US]:
S3 Endpoint [s3.amazonaws.com]: localhost:9000
DNS-style bucket+hostname:port template for accessing a bucket [%(bucket)s.s3.amazonaws.com]: localhost:9000
Encryption password:
Path to GPG program [/usr/bin/gpg]:
Use HTTPS protocol [Yes]: no
```

```bash
# observar todos los buckets
s3cmd --config minio.s3cfg la 
```



### DATALAKE CREATE SCHEMA IN BASE OF THE FOLDER

### CREATE  A TABLE USING THE JSON FILE UPLOADED
```bash 
./trino --execute "\
CREATE SCHEMA IF NOT EXISTS minio.localstorage \
WITH (location = 's3a://localstorage/');\
DROP TABLE IF EXISTS minio.localstorage.json_file;"

./trino --execute "CREATE TABLE minio.localstorage.json_file(\
    name VARCHAR,\
    population DOUBLE,\
    area DOUBLE)\
WITH ( external_location = 's3a://localstorage/borough_data/', format = 'json');"

./trino --execute "SHOW TABLES FROM minio.localstorage;"
./trino --execute "SELECT * FROM minio.localstorage.json_file;"
```
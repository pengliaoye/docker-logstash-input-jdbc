```sql
CREATE TABLE `content_news` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `title` varchar(1000) DEFAULT NULL COMMENT '标题',
  `content` varchar(5000)  DEFAULT NULL COMMENT '内容',
  `created_at` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  `is_deleted` tinyint(255) unsigned DEFAULT NULL COMMENT '1 是  0 否',
  PRIMARY KEY (`id`)
);
```
## Starting logstash 

Starting logstash input jdbc

```bash
docker-compose -f compose/mysql/docker-compose.mysql-5.7.yaml -p prj up --build
```

Stoping all service

```bash
docker-compose -f compose/mysql/docker-compose.mysql-5.7.yaml -p prj down --rmi local
```

## Customize configuration

Customize configuration for logstash

- modify file `logstash.conf` in docker container

```yml
input {
    jdbc {
        jdbc_driver_library => "${LOGSTASH_JDBC_DRIVER_JAR_LOCATION}"
        jdbc_driver_class => "${LOGSTASH_JDBC_DRIVER}"
        jdbc_connection_string => "${LOGSTASH_JDBC_URL}"
        jdbc_user => "${LOGSTASH_JDBC_USERNAME}"
        jdbc_password => "${LOGSTASH_JDBC_PASSWORD}"
        schedule => "* * * * *"
        statement => "select ... from table_name"
    }
}

output {
    stdout { codec => json_lines }
}
```

Environtment example

- LOGSTASH_JDBC_DRIVER_JAR_LOCATION: 
    - for MySQL 5.7+ Database: `/usr/share/logstash/logstash-core/lib/jars/mysql-connector-java.jar`
    - for MS-SQL Server 2017 linux+ Database: `/usr/share/logstash/logstash-core/lib/jars/mssql-jdbc.jar`
    - for Oracle 11g XE Database: `/usr/share/logstash/logstash-core/lib/jars/ojdbc6.jar`
    - for PostgreSQL 9.6+ Database: `/usr/share/logstash-core/lib/jars/logstash/postgresql.jar`
- LOGSTASH_JDBC_DRIVER: `com.mysql.jdbc.Driver`
- LOGSTASH_JDBC_URL: `jdbc:mysql://[host]:[port]/[database-name]`
- LOGSTASH_JDBC_USERNAME: `database_user`
- LOGSTASH_JDBC_PASSWORD: `database_user_password`
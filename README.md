# Overview

The SP integration library for CIP .

The latest version is [0.9.0]

# Gradle 

## spase 

```
	compile com.soterianetworks:spase-core:0.9.0
	compile com.soterianetworks:spase-starter:0.9.0
```

## 3rd

```
	compile org.hibernate:hibernate-core:5.1.17.Final
	compile org.hibernate:hibernate-validator:5.1.3.Final
	compile org.hibernate.javax.persistence:hibernate-jpa-2.1-api:1.0.2.Final
	compile javax.validation:validation-api:1.1.0.Final
	compile mysql:mysql-connector-java:5.1.47
```

> fasterxml & jackson & hibernate will be auto imported with spring-boot

## auto imported

* commons-logging:commons-logging:1.1.3
* org.apache.commons:commons-lang3:3.8.1
* com.google.guava:guava:21.0
* org.torpedoquery:org.torpedoquery:2.5.1

# Configuration

## application.yml

```aidl
spring:
  jpa:
    show-sql: true
    hibernate:
      ddl-auto: none
    properties:
      hibernate:
        dialect: ${JPA_DIALECT:org.hibernate.dialect.MySQL5Dialect}
      javax:
        persistence:
          query:
            timeout: 300000
  datasource:
    url:  ${DB_URL:jdbc:mysql://127.0.0.1:3306/spase_database?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true}
    driver-class-name: ${DB_DRIVER:com.mysql.jdbc.Driver}
    username:  ${DB_USER:spase_user}
    password:  ${DB_PASSWORD:spase_password}

logging:
  level:
    root: INFO
    org.torpedoquery.jpa: DEBUG
    
```

## Spring Boot Application

Supply your ResourceBundleMessageSource. 

```
    @Bean
    public ReloadableResourceBundleMessageSource messageSource() {
        ReloadableResourceBundleMessageSource messageSource = new ReloadableResourceBundleMessageSource();
        messageSource.setBasename("classpath:locale/messages");
        messageSource.setCacheSeconds(3600); //refresh cache once per hour

        return messageSource;
    }

```

# Development Guide

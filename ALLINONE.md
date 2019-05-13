# Gradle 

## spase 

```groovy
compile("com.soterianetworks:spase-std:0.9.10")
```
## imported by spase

* commons-logging:commons-logging:1.1.3
* org.apache.commons:commons-lang3:3.8.1
* com.google.guava:guava:21.0

# Configuration

## application.yml

```yml
spring:


```

## Spring Boot Application

## I18n

### Message

Supply your ResourceBundleMessageSource (Which helps to generate the i18n Error Response )

```java

@Configuration
public class XxxWebMvcConfigurer extends WebMvcConfigurerAdapter {

}

```

### Enum


Here is the I18nEnum definition:

```java
package com.soterianetworks.spase.domain.enums.i18n;

import java.util.Map;

public interface I18nEnum {

    Map<String, String> toMap();

    String getEn();

    String getZh();

}
```

Supply your I18nEnum implementation , for example:

```java
public enum MessageStatus implements I18nEnum {

    UNREAD("UNREAD", "未读"),

    READ("READ", "已读");

    private String en;

    private String zh;

    MessageStatus(String en, String zh) {
        this.en = en;
        this.zh = zh;
    }

    @Override
    public Map<String, String> toMap() {
        return ImmutableMap.<String, Object>builder()
                .put("en", en)
                .put("zh", zh)
                .build();
    }

    @Override
    public String getEn() {
        return en;
    }

    @Override
    public String getZh() {
        return zh;
    }

}


```


Configure the Spring Boot Application with:


```java

@Bean
public I18nEnumRegistry I18nEnumRegistry() {
	I18nEnumRegistry registry = new I18nEnumRegistry();
	registry.setScanPackage("put your package which contains the i18n enum impl");
	return registry;
}

```

> All the auto-registered I18nEnum instances can be retrieved by `I18nEnumRegistry#getEnums`


## Interceptor

Enable SP Request Interceptor (Which helps to generate the TenantContext for each Rest request)

```java

@Configuration
public class XxxWebMvcConfigurer extends WebMvcConfigurerAdapter {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(spRequestInterceptor())
                .excludePathPatterns("put what you want to exclude")
                .addPathPatterns("/**");
    }

    @Bean
    public SpRequestInterceptor spRequestInterceptor() {
        return new SpRequestInterceptor();
    }

}

```


## Others

```java

@Configuration
public class XxxWebMvcConfigurer extends WebMvcConfigurerAdapter {

    @Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        configurer.setUseSuffixPatternMatch(false);
    }

    @Bean
    public FilterRegistrationBean characterEncodingFilter() {
        FilterRegistrationBean registration = new FilterRegistrationBean();
        CharacterEncodingFilter filter = new CharacterEncodingFilter();
        filter.setEncoding("UTF-8");
        registration.setFilter(filter);
        return registration;
    }

    @Bean
    public LocaleResolver acceptHeaderLocaleResolver() {
        AcceptHeaderLocaleResolver resolver = new AcceptHeaderLocaleResolver();
        resolver.setDefaultLocale(new Locale("en_US"));
        return resolver;
    }

    @Bean
    public PasswordEncoder bCryptPasswordEncoder() {
        return new BCryptPasswordEncoder();
    }
    
```

# Development Guide

## Shared API

| Package | Class | Description |
|---|---|---|
| com.soterianetworks.spase.domain.model | Tenant
| | Benity
| | Department
| | Group
| | User
| com.soterianetworks.spase.domain.request | BenitySearchRequest
| |  DepartmentSearchRequest
| |  PageableSearchRequest
| |  QuickSearchRequest
| |  TenantSearchRequest
| com.soterianetworks.spase.domain.support |BaseSearchParam
| |  DepartmentSearchParam
| |  PageableSearchParam
| com.soterianetworks.spase.service  | DepartmentService |
| | UserProfileService|
| com.soterianetworks.spase.service.validator | BenityValidator |
| | DepartmentValidator|
| | TenantValidator|
| | UserValidator|
| com.soterianetworks.spase.repository | BenityRepository |
| | DepartmentRepository |
| | GroupRepository |
| | TenantRepository |
| | UserRepository |
| com.soterianetworks.spase.repository.custom |  AbstractCustomRepository |     

## Inject Tenant Context in Web Layer

```java

public class RestControllerSupport  {
    
	@Autowired
	private TenantContext tenantContext;

}

```

```java
public interface TenantContext {

    Tenant getTenant();

    Benity getBenity();

    User getUser();

}

```

## Enable Exception Handler

```java
@ControllerAdvice(annotations = {RestController.class})
public class MyExceptionHandler extends SpExceptionHandler {
}
```

## Customized Repository Sample

Please follow the [Torpedo Query](https://torpedoquery.org/) API Spec.


```java

@Repository
public class DepartmentRepositoryImpl extends AbstractCustomRepository implements DepartmentCustomRepository {

    @Override
    public Page<Department> search(DepartmentSearchRequest searchRequest) {
        Pageable pageable = searchRequest.resolvePageable();

        Department from = from(Department.class);

        OnGoingLogicalCondition condition = condition(from.getId()).isNotNull();

        if (!StringUtils.isEmpty(searchRequest.getCode())) {
            condition.and(from.getCode()).like().any(searchRequest.getCode());
        }
        if (!StringUtils.isEmpty(searchRequest.getName())) {
            condition.and(from.getName()).like().any(searchRequest.getName());
        }

        where(condition);

        Query<Long> countQuery = select(count(from)).freeze();
        Query<Department> listQuery = select(from);

        long total = countQuery.get(entityManager).orElse(0L);

        if ("code".equalsIgnoreCase(searchRequest.getSortBy())) {
            orderBy(Sort.Direction.ASC == searchRequest.getSortDirection() ?
                    asc(from.getCode()) :
                    desc(from.getCode()));
        }
        

        List<Department> content = listQuery.setFirstResult(pageable.getOffset())
                                            .setMaxResults(pageable.getPageSize())
                                            .list(entityManager);

        return PageableExecutionUtils.getPage(content, pageable, () -> total);

    }

}


```

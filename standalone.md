# Build 

## Gradle - build.gradle 


```groovy
dependencies {
	//...
	
	compile("com.soterianetworks:spase-standalone:0.9.18")
	compile("com.soterianetworks:spase-standalone-starter:0.9.18")
	
	//...
}
```

## Maven - pom.xml

```xml
<dependencies>

	<dependency>
		<groupId>com.soterianetworks</groupId>
		<artifactId>spase-standalone</artifactId>
		<version>0.9.18</version>
	</dependency>

	<dependency>
		<groupId>com.soterianetworks</groupId>
		<artifactId>spase-standalone-starter</artifactId>
		<version>0.9.18</version>
	</dependency>

</dependencies>

```

## Spase Spring Boot Starter

Enable Spase Standalone Spring Boot Starter by adding `com.soterianetworks:spase-standalone-starter` to build path

> spring.factories 

```ini
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.soterianetworks.spase.starter.SpaseAutoConfiguration
```

> com.soterianetworks.spase.starter.SpaseAutoConfiguration
> 1. auto scan the package `com.soterianetworks.spase`
> 2. auto enable `SpaseProperties` with prefix `spase.client`

```java
@Configuration
@ComponentScan({"com.soterianetworks.spase"})
@EnableConfigurationProperties(SpaseAutoConfiguration.MySpaseProperties.class)
public class SpaseAutoConfiguration {

    @ConfigurationProperties("spase.client")
    public class MySpaseProperties extends DefaultSpaseProperties {

    }

}
```

And it imports following configurations automatically

* com.soterianetworks.spase.starter.config.CacheConfigurer
* com.soterianetworks.spase.config.FeignClientConfigurer

# Configuration

## Spase Properties

Prefix in `application.yml`

> spase.client

Key | Default Value | Description
---|---|---
gatewayHost | | Required
userChangedChannel | cip_user_changed | The Redis PUB/SUB channel name 
reignLogLevel | Logger.Level.BASIC | Netflix Reign Log Level
readTimeoutInSecond | 60 | Work for reign client
writeTimeoutInSecond | 60 | Work for reign client
connectTimeoutInSecond | 120 | Work for reign client


## Spring `application.yml`

> Common

```yml

# We are using okhttp 
feign.httpclient.enabled: false

# Change to match your actual env
spring:
  redis:
    host: 127.0.0.1
    port: 6379

logging:
  level:
    root: INFO
    feign: DEBUG
    
```

> Specify the CIP gateway for SP

```yml
spase.client:
  gatewayHost: https://www.icwind.net/gateway
```

## Spring Security Configuration

The Spase Standalone lib is designed as a standard Spring Security provider.

> The `SpaseAuthenticationFilter` implements `OncePerRequestFilter` and `SecurityContextHolder` is ready by hand.
		
```java
@Configuration
public class WebSecurityConfigurer extends WebSecurityConfigurerAdapter {

    @Bean
    public UserDetailsService userDetailsServiceImpl() {
        return new UserDetailsServiceImpl();
    }

    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().antMatchers("/", "/health", "/info", "/favicon.ico", "/error");
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.addFilterBefore(new SpaseAuthenticationFilter(userDetailsServiceImpl()),
                             BasicAuthenticationFilter.class);

        http.csrf()
            .and()
            .authorizeRequests().anyRequest().authenticated()
            .and()
            .formLogin().disable();
    }

}
```

Then you can get the Authenticated User Details in the way of Spring Security.

```java
SecurityContextHolder.getContext().getAuthentication().getDetails();
```

## Create your own SP application

```java
@SpringBootApplication
public class StandaloneSpApplication {

    public static void main(String[] args) {
        SpringApplication.run(StandaloneSpApplication.class, args);
    }

}
```


# Appendix - Spase Standalone Spring Security Implementations


```
interface `com.soterianetworks.spase.security.support.UserDetailsService`
 	implementation `com.soterianetworks.spase.security.support.UserDetailsServiceImpl`
```

```
interface `org.springframework.web.filter.OncePerRequestFilter`
	implementation `com.soterianetworks.spase.security.filter.SpaseAuthenticationFilter`
		dependsOn `com.soterianetworks.spase.security.support.UserDetailsService`
```

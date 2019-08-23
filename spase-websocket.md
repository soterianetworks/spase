# Spase Web Socket Get Started

Please get the latest version from 

> https://mvnrepository.com/artifact/com.soterianetworks/spase-ws-starter

## Dependencies


* maven - pom.xml

> extends spring boot starter parent 

```xml
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
	</dependency>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-websocket</artifactId>
	</dependency>

	<dependency>
		<groupId>com.soterianetworks</groupId>
		<artifactId>spase-ws-starter</artifactId>
		<version>${spase.ws.version}</version>
	</dependency>
</dependencies>	
```

* gradle - build.gradle

> enable gradle spring boot plugin

```gradle
	compile "org.springframework.boot:spring-boot-starter-web"
	compile "org.springframework.boot:spring-boot-starter-websocket"
	
	compile "com.soterianetworks:spase-ws-starter:${spase-ws-version}"
```

## API Usage Sample

1. inject dispatcher

```java

public class YourService {

    @Autowired
    private MessageDispatcher messageDispatcher;

}

```

2. send message to target

target type | param | description 
---|---|---
user | the user id or user name| message to specified user
broadcast | the topic name | also named `topic`, all the client who subscribed the topic will receive the message
queue | the queue name | only one of the client who subscribed the queue will receive the message


```java
public class YourService {

    @Autowired
    private MessageDispatcher messageDispatcher;
    
	private Resource photo = new ClassPathResource("/binary-sample-file.png");

    @Override
    public void sendToUser() {
        MessageBuilder.user("123456789").to("tasks").body(new UserMessage()).accept(messageDispatcher);
    }

    @Override
    public void sendToTopic() {
        MessageBuilder.broadcast("warning").body(new TopicMessage()).accept(messageDispatcher);
    }

    @Override
    public void sendToQueue() {
        MessageBuilder.queue("tasks").body(new QueueMessage()).accept(messageDispatcher);
    }

    @Override
    public void sendText() {
        MessageBuilder.queue("text").text("Hello Format").accept(messageDispatcher);
    }

    @Override
    public void sendJson() {
        MessageBuilder.queue("json").body(new TopicMessage()).accept(messageDispatcher);
    }

    @Override
    public void sendBinary() {
        try {
            byte[] data = new byte[1024];
            IOUtils.readFully(photo.getInputStream(), data);
            MessageBuilder.queue("binary").binary(data).accept(messageDispatcher);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void sendBase64() {
        MessageBuilder.queue("base64").base64("Hello Binary".getBytes()).accept(messageDispatcher);
    }
    
}
        
```

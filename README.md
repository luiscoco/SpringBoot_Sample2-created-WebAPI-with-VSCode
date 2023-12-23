# How to create a SpringBoot Web API in VSCode

You can find this example source code in this github repo: https://github.com/luiscoco/SpringBoot_Sample2-created-WebAPI-with-VSCode

## 1. Set up the environment

First, ensure you have the following setup in your VS Code environment:

- Java Development Kit (**JDK**)

- **VS Code** with the Java extension pack installed

- **Maven** for dependency management

## 2. Project folders structure

![image](https://github.com/luiscoco/SpringBoot_Sample2-created-WebAPI-with-VSCode/assets/32194879/15efd028-41f0-46f3-bf6f-6856546f393e)

![image](https://github.com/luiscoco/SpringBoot_Sample2-created-WebAPI-with-VSCode/assets/32194879/d7cf549c-03c9-44ac-b340-9d5b12404d88)

## 3. Here's a simple Java Spring Boot application source code

**pom.xml**

```xml 
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>demoapi</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>demoapi</name>
    <description>Demo project for Spring Boot</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.0</version>
        <relativePath/>
    </parent>

    <dependencies>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

**DemoapiApplication.java**

```java
package com.example.demoapi;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoapiApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoapiApplication.class, args);
    }
}
```

**HelloController.java**

http://localhost:8080/hello

```java
package com.example.demoapi.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {
    @GetMapping("/hello")
    public String sayHello() {
        return "Hello, World!";
    }
}
```

## 4. Dockerfile

```
# Start with a base image containing Java runtime
FROM openjdk:11-jdk-slim as build

# Add Maintainer Info
LABEL maintainer="your_email@example.com"

# Add a volume pointing to /tmp
VOLUME /tmp

# Make port 8080 available to the world outside this container
EXPOSE 8080

# The application's jar file
ARG JAR_FILE=target/demoapi-0.0.1-SNAPSHOT.jar

# Add the application's jar to the container
ADD ${JAR_FILE} demoapi.jar

# Run the jar file
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/demoapi.jar"]
```

## 5. To build and package the application

To build the application execture this command in the VSCode terminal window:

```
mvn clean install
```

![image](https://github.com/luiscoco/SpringBoot_Sample2-created-WebAPI-with-VSCode/assets/32194879/2e29db06-e971-47d7-babe-13b51125943e)

To run the application run this command:

```
java -jar target/demoapi-0.0.1-SNAPSHOT.jar
```

## 6. How build the Docker image and run it

To build the Docker image execute this command:

```
docker build -t demoapi .
```

To run the Web API docker image run this command:

```
docker run -p 8080:8080 demoapi
```

![image](https://github.com/luiscoco/SpringBoot_Sample2-created-WebAPI-with-VSCode/assets/32194879/f01af46d-595f-470b-b07d-68d17bc98e7c)


## 7. How to create this example with ChatGPT-4

- Please provide me a Java API code sample in VSCode

- Please give me the whole application in different files

- Is it possible you compress the above files in a zip file to download them?

- Please tell me the command to execute this project in VSCode

- Can you provide me the Dockerfile to create a docker image for the above application API?

- Please provide me the command to run the docker image and access to the endpoint in my internet web browser

- Can you provide me the deployment.yml and service.yml files to deploy the application in AWS EKS?

## 8. How deploy the Web API in AWS EKS

To deploy your application in AWS Elastic Kubernetes Service (EKS), you'll need to create two YAML files: one for the deployment (**deployment.yml**) and one for the service (**service.yml**). 

Below are sample YAML files that you can use and modify according to your requirements.

**Deployment YAML (deployment.yml)**

This file defines the deployment of your application in the Kubernetes cluster.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demoapi-deployment
spec:
  replicas: 2  # The number of Pods to run
  selector:
    matchLabels:
      app: demoapi
  template:
    metadata:
      labels:
        app: demoapi
    spec:
      containers:
        - name: demoapi
          image: <your-docker-image>  # Replace with your Docker image, e.g., "username/demoapi:latest"
          ports:
            - containerPort: 8080
```

Replace **<your-docker-image>** with the path to your Docker image in your container registry.

**Service YAML (service.yml)**

This file defines how your application is exposed.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: demoapi-service
spec:
  type: LoadBalancer  # Exposes the service externally using a load balancer
  selector:
    app: demoapi
  ports:
    - protocol: TCP
      port: 80  # The port the load balancer listens on
      targetPort: 8080  # The port the container accepts traffic on
```

**Deploying to AWS EKS**

After creating these files, you can use the kubectl command-line tool to apply these configurations to your EKS cluster:

Deploy the Application:

```
kubectl apply -f deployment.yml
```

**Create the Service**

```
kubectl apply -f service.yml
```

Verify the Deployment:

Check the status of the deployment:

```
kubectl get deployments
```

Check the status of the pods:

```
kubectl get pods
```

Access the Service:

Once the service is deployed, it will get an external IP address.

You can find the external IP address with:

```
kubectl get service demoapi-service
```

Use the external IP address to access your application in a web browser: **http://<EXTERNAL-IP>/hello**

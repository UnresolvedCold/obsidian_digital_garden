---
{"dg-publish":true,"permalink":"/garden/run-surefire-tests-in-parallel-in-different-jvm/","tags":["insight","java","JUnit"]}
---

# POM changes

You can add the below configuration to enable JVM in multiple threads and run each test in it's own JVM.

This will also prevent singleton classes from being shared for each test case. 

```xml
<build>  
  <plugins>  
    <plugin>  
      <groupId>org.apache.maven.plugins</groupId>  
      <artifactId>maven-surefire-plugin</artifactId>  
      <version>3.0.0</version>  
      <configuration>   
        <!-- Parallel execution config -->  
        <parallel>classes</parallel>  
        <threadCount>8</threadCount>  
        <forkCount>8</forkCount> <!-- JVMs running in parallel -->  
        <reuseForks>false</reuseForks> <!-- Fresh JVM per test class -->  
  
        <useUnlimitedThreads>true</useUnlimitedThreads>  
        <perCoreThreadCount>false</perCoreThreadCount>  
      </configuration>  
    </plugin>  
  </plugins>  
</build>
```

## Exclude any class from running in parallel

You can mark the test class as `@Execution(ExecutionMode.SAME_THREAD)` to not execute in parallel.

```java
@Execution(ExecutionMode.SAME_THREAD)  
public class RelayAPITest extends AbstractRelayManagerTestSetup {
  ...
}
```

For this to work, you will also need to enable parallel execution in JUnit. 
Create a file `src/test/resources/junit-platform.properties` and the below lines. 

```properties
# Enable parallel execution  
junit.jupiter.execution.parallel.enabled = true  
  
# Parallel at class level  
junit.jupiter.execution.parallel.mode.default = concurrent  
junit.jupiter.execution.parallel.mode.classes.default = concurrent  
  
# Use all available CPU threads  
junit.jupiter.execution.parallel.config.strategy = dynamic
```

### Prevent 2 Test Cases to run in parallel

This may be important when you are testing an API server which is exposed at a specific port. 
And you do not want to change the port. 
In this case you can take a resource lock as follows.

> This only works across threads but not across forks

```java
@ResourceLock("MVTS_SERVER")
public class TC1 {
  public void test1() {}
}

@ResourceLock("MVTS_SERVER")
public class TC2 {
  public void test2() {}
}
```

---
{"dg-publish":true,"permalink":"/garden/install-a-different-version-of-library-using-command-line/","tags":["insight"]}
---

## Define the version as a property 

```xml
<properties>  
  <java.version>17</java.version>  
  <mapf.version>3eabb03-SNAPSHOT</mapf.version>  
</properties>

<dependencies>
	<dependency>  
	  <groupId>com.greyorange</groupId>  
	  <artifactId>mapf</artifactId>  
	  <version>${mapf.version}</version>  
	</dependency>
</dependencies>
```

## Command to install a different version 

The below command will install `1.0-SNAPSHOT`
```bash
mvn clean install -Dmapf.version=1.0-SNAPSHOT
```
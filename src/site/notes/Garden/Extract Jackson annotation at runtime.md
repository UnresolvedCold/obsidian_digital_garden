---
{"dg-publish":true,"permalink":"/garden/extract-jackson-annotation-at-runtime/","tags":["insight","java","jackson","reflection"]}
---


# Extracting Jackson annotation 
```java
public static void main(String[] args) throws Exception {    
    enum Items {  
        @JsonProperty("home.kitchen.bread")  
        BREAD,  
        @JsonProperty("home.kitchen.milk")  
        MILK,  
        @JsonProperty("home.bedroom.pillow")  
        PILLOW,  
        @JsonProperty("home.bedroom.sheets")  
        SHEETS,  
        @JsonProperty("home.livingroom.couch")  
        COUCH,  
        @JsonProperty("home.livingroom.tv")  
        TV  
    }  

	// Extract annotation 
    Field field = Items.class.getField(Items.MILK.name());  
    JsonProperty annotation = field.getAnnotation(JsonProperty.class);  
	System.out.println(annotation.value());  
  }
```

###### Output
```bash 
home.kitchen.milk

```

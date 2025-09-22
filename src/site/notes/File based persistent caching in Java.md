---
{"dg-publish":true,"permalink":"/file-based-persistent-caching-in-java/","tags":["compilation","insight","java","MapDB","Caching","Persistent","DB","ACID","MemoryMapped","MMap","File"]}
---

# File based persistent caching in Java

The fasted way to keep a cached state is storing the values in an in-memory cache. And it works all good until your app crashes and your state is completely lost. 

The best way to store your cached values is using a different server than your application server.
But when you app is small and you do not want all that trouble of maintaining multiple pods to serve your business throughput. And when you do now want to add too many lines of code too soon. And when your cache is just few mega bytes in size. You can just store your state in a file. 

And the best way to store your cache in a persistent file is using [[memory mapped file\|memory mapped file]] in Java. 
This I think is a good compromise b/w simplicity and speed. You do not need a huge code change and you can start building your application with all the test cases you need. 

## Memory mapped cache using MapDB

[MapDB](https://mapdb.org/) is an open-source file based storage solution that has build in capabilities to read/write memory mapped files. 

If you are using maven, you can just add a dependency.
```xml
<dependency>
    <groupId>org.mapdb</groupId>
    <artifactId>mapdb</artifactId>
    <version>VERSION</version>
</dependency>
```

The API provides you with `DBMaker` class which can be configured as you like. 

Here, I'm configuring the DB to store data inside file `cache.ch`. With the function `fileMmapEnable`, I'm enabling MMap on the file. And there are other configurations. 

> You can aslo encrypt your DB using a password, not covering this here.
> transactionEnable() enables WAL (write ahead log) which is slower but reliable 

```java
DB db = DBMaker  
    .fileDB("cache.ch")  
    .fileMmapEnable()  
    .transactionEnable()  
    .closeOnJvmShutdown()  
    .make();
```

A DB may contain multiple collections or tables. `HTreeMap` is one of the collections which provides a hash map functionality. 

```java
HTreeMap<String, Long> map = db.hashMap("usercache")
        .keySerializer(Serializer.STRING)
        .valueSerializer(Serializer.LONG)
        .create();
```

> There is also an expire functionality based on duration 

## Cache Implementation

Not going too in-detail. Here is the interface for my caching framework.

```java
public interface ICache<K, V> {  
  public V get(K key);  
  public void put(K key, V value);  
  public boolean containsKey(K key);  
  public void invalidate(K key);  
  public void clear();  
  public Map<K, V> getAll();  
  public List<V> getAllValues();  
  public List<K> getAllKeys();  
}
```

 The in-memory cache is just a concurrent hash map and persistent cache is HTreeMap which is stored and serialized in a [[memory mapped file\|memory mapped file]].

```java 
public class PersistentMMapCache<K, V> implements ICache<K, V> {  
  private final HTreeMap<K, V> map;  
  
  public PersistentMMapCache(String collectionName) {  
  
    this.map = PersistentDBProvider.getInstance().getDB()  
        .hashMap(collectionName)  
        .keySerializer(org.mapdb.Serializer.JAVA)  
        .valueSerializer(org.mapdb.Serializer.JAVA)  
        .createOrOpen();  
  }  
  @Override  
  public V get(K key) {  
    return map.get(key);  
  }  
  @Override  
  public void put(K key, V value) {  
    map.put(key, value);  
    PersistentDBProvider.getInstance().getDB().commit();  
  }  
  @Override  
  public boolean containsKey(K key) {  
    return map.containsKey(key);  
  }  
  @Override  
  public void invalidate(K key) {  
    map.remove(key);  
    PersistentDBProvider.getInstance().getDB().commit();  
  }  
  @Override  
  public void clear() {  
    map.clear();  
    PersistentDBProvider.getInstance().getDB().commit();  
  }  
  @Override  
  public Map<K, V> getAll() {  
    return Collections.unmodifiableMap(new HashMap<>(map));  
  }  
  @Override  
  public List<V> getAllValues() {  
    return new ArrayList<>(map.values());  
  }  
  @Override  
  public List<K> getAllKeys() {  
    return new ArrayList<>(map.keySet());  
  }  
```

The DB provider is transactional singleton class as follows. 

```java
private final DB db;  
  
private PersistentDBProvider() {  
  this.db = DBMaker  
      .fileDB(getDBFilePath())  
      .fileMmapEnable()  
      .transactionEnable()  // Write ahead log  
      .closeOnJvmShutdown()  
      .make();  
}
```

## Performance comparison

I wrote a small script to compare the speed of transactions and here are the results.

```java
long runSpeedTest(ICache<Integer, Integer> cache, int operations) {  
  long startTime = System.currentTimeMillis();  
  for (int i = 0; i < operations; i++) {  
    cache.put(i, i);  
  }  
  long endTime = System.currentTimeMillis();  
  System.out.println("Write " + operations + " operations: " + (endTime - startTime) + " ms");  
  
  startTime = System.currentTimeMillis();  
  for (int i = 0; i < operations; i++) {  
    cache.get(i);  
  }  endTime = System.currentTimeMillis();  
  System.out.println("Read " + operations + " operations: " + (endTime - startTime) + " ms");  
  
  return endTime - startTime;  
}
```

This speed test was performed on 100000 entries and below are the results. 

| Cache implemenatation             | Write     | Read   |
| --------------------------------- | --------- | ------ |
| Concurrent Hash Map (in-memory)   | 8 ms      | 4 ms   |
| HTreeMap (persistent) without WAL | 50441 ms  | 494 ms |
| HTreeMap (persistent) with WAL    | 346083 ms | 580 ms |
> This speed depends on the hardware you are using. I'm using macbook M1 Air 2020 model which has 256GB SSD storage.  


## Conclusions 

Memory mapped file storage is significantly slower than concurrent hash maps which is expected. With WAL enabled, the speed decreases further. Whether to go with in-memory cache or persistent cache must be a justified decision. 

---
layout: post
title: Spring注解式cache机制
tags: Spring
---

# Spring注解式cache机制  

## 缓存概述  

 ![缓存](/images/overview.png "缓存")


## Spring cache概述
Spring的cache是对一种缓存使用场景的抽象:  

> **有限的** KV结构的数据,在更新/删除时刷新,未命中需要加载的 **本地缓存**  

通过注解配置cache的分类,SPEL式的key表达式和缓存条件表达式,通过AOP的方式在调用时执行缓存加载/刷新/清除逻辑。  
由于结构限制,无法简单实现过期加载。

## 缓存管理器实现  
* 接口`CacheManager`及抽象类`AbstractCacheManager`:

```java
public interface CacheManager {

    /**
     * Return the cache associated with the given name.
     * @param name cache identifier (must not be {@code null})
     * @return associated cache, or {@code null} if none is found
     */
    Cache getCache(String name);

    /**
     * Return a collection of the caches known by this cache manager.
     * @return names of caches known by the cache manager.
     */
    Collection<String> getCacheNames();

}    
```

* 作为全局的缓存管理器, `CacheManager` 的作用是管理 `cacheNames`,既每个缓存使用时机标识,默认实现是在bean初始化结束后,初始化`cacheMap`。

```java
public abstract class AbstractCacheManager implements CacheManager, InitializingBean {

    private final ConcurrentMap<String, Cache> cacheMap = new ConcurrentHashMap<String, Cache>();

    private Set<String> cacheNames = new LinkedHashSet<String>();


    public void afterPropertiesSet() {
        Collection<? extends Cache> caches = loadCaches();
        Assert.notEmpty(caches, "loadCaches must not return an empty Collection");
        this.cacheMap.clear();

        // preserve the initial order of the cache names
        for (Cache cache : caches) {
            this.cacheMap.put(cache.getName(), cache);
            this.cacheNames.add(cache.getName());
        }
    }

    protected final void addCache(Cache cache) {
        this.cacheMap.put(cache.getName(), cache);
        this.cacheNames.add(cache.getName());
    }

    public Cache getCache(String name) {
        return this.cacheMap.get(name);
    }

    public Collection<String> getCacheNames() {
        return Collections.unmodifiableSet(this.cacheNames);
    }


    /**
     * Load the caches for this cache manager. Occurs at startup.
     * The returned collection must not be null.
     */
    protected abstract Collection<? extends Cache> loadCaches();

}  
```

## 缓存结构

接口`Cache`定义了缓存结构需要实现的功能:    
```java
public interface Cache {

    /**
     * Return the cache name.
     */
    String getName();

    /**
     * Return the the underlying native cache provider.
     */
    Object getNativeCache();

    /**
     * Return the value to which this cache maps the specified key. Returns
     * <code>null</code> if the cache contains no mapping for this key.
     * @param key key whose associated value is to be returned.
     * @return the value to which this cache maps the specified key,
     * or <code>null</code> if the cache contains no mapping for this key
     */
    ValueWrapper get(Object key);

    /**
     * Associate the specified value with the specified key in this cache.
     * <p>If the cache previously contained a mapping for this key, the old
     * value is replaced by the specified value.
     * @param key the key with which the specified value is to be associated
     * @param value the value to be associated with the specified key
     */
    void put(Object key, Object value);

    /**
     * Evict the mapping for this key from this cache if it is present.
     * @param key the key whose mapping is to be removed from the cache
     */
    void evict(Object key);

    /**
     * Remove all mappings from the cache.
     */
    void clear();


    /**
     * A (wrapper) object representing a cache value.
     */
    interface ValueWrapper {

        /**
         * Return the actual value in the cache.
         */
        Object get();
    }

}
```
需要实现缓存记录的put/get/evict/clear等方法。  

## AOP加载配置
![Advisor](/images/diagram.png "Adviser")   

## 缓存调用流程
![缓存调用流程](/images/缓存调用流程.png "缓存调用流程")

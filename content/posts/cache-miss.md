---
title: Cache Miss
date: 2022-01-14 00:00:00.0
summary: Spring cache miss scenarios and solutions.
categories: ["Tech"]
tags: ["Tech"]
---

## Cache Avalanche

Most caches expire at the same time.

Solution: 

- Use random ttl.
```java
// use ttl function instead of fixed duration.
cacheConfiguration.entryTtl((k, v) -> 
    Duration.ofSeconds(seconds + ThreadLocalRandom.current().nextInt(seconds / 2))
);
```

## Cache Breakdown

Hotkey caches expire, causing lots of requests to hit the database.

Solution:

- Use a lock to query data from the database.
```java
// use sync just for single jvm.
// use distributed lock for cluster.
@Cacheable(cacheNames = "product", key = "#id", sync = true)
public Product getProduct(Long id) {
    return productRepository.findById(id);
}
```
- Hot key caches never expire, refresh them async.

## Cache Penetration

The key does not exist in either cache or database.

Solution:

- Validate request parameters.
- Cache null values.
  ```yaml
  spring:
    cache:
      redis:
        cache-null-values: true
  ```

  Or enable in code.
  ```java
  RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig().cacheNullValues();
  ```
- Use a Bloom Filter (guava) to filter out non-existent keys.

```java
@Component
public class UserBloomFilter {
    private final BloomFilter<Long> bloomFilter =
        BloomFilter.create(Funnels.longFunnel(), 1_000_000, 0.01);

    @PostConstruct
    public void init() {
        userRepository.findAllIds().forEach(bloomFilter::put);
    }

    public boolean mightContain(Long id) {
        return bloomFilter.mightContain(id);
    }
}


public User getUser(Long id) {
    if (!bloomFilter.mightContain(id)) {
        return null;
    }
    return userRepository.findById(id).orElse(null);
}
```

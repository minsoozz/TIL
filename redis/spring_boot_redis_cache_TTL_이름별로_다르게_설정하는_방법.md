# Redis Cache TTL 이름별로 다르게 설정하는 방법

기존 애플리케이션에 적용된 조회 API의 `Cache`기능 이외에 조회마다 일어나는 조회수의 기능도 `Cache` 기능이 필요하다고 생각하였고,
TTL 정책을 다르게 주어 유연하게 `Cache`기능을 사용하고 싶었다.

### TTL 설정

---

`Redis`는 기본적으로 `RedisCacheManager`의 아래의 메소드들을 통해 `RedisCacheConfiguration`를 전달해서 설정할 수 있다.

- cacheDefaults(RedisCacheConfiguration defaultCacheConfiguration)
- withInitialCacheConfigurations(Map<String, RedisCacheConfiguration> cacheConfigurations)

### Configuration 설정

---

default 설정과 config 설정은 각각 작성하였고, `withInitialCacheConfigurations` 에서 관리하지 않는 `cacheName`은 모두 `cacheDefaults` 설정을 따른다.

```
@Configuration
@RequiredArgsConstructor
public class CacheConfig {

  private final RedisConnectionFactory connectionFactory;

  @Bean
  public RedisCacheManager redisCacheManager() {

    return RedisCacheManager
        .RedisCacheManagerBuilder
        .fromConnectionFactory(connectionFactory)
        .cacheDefaults(defaultConfiguration())
        .withInitialCacheConfigurations(customConfigurationMap())
        .build();
  }

  private RedisCacheConfiguration defaultConfiguration() {
    return RedisCacheConfiguration.defaultCacheConfig()
        .serializeKeysWith(fromSerializer(new StringRedisSerializer()))
        .serializeValuesWith(fromSerializer(new GenericJackson2JsonRedisSerializer()))
        .entryTtl(Duration.ofMinutes(1));
  }

  private Map<String, RedisCacheConfiguration> customConfigurationMap() {
    Map<String, RedisCacheConfiguration> customConfigurationMap = new HashMap<>();
    customConfigurationMap.put(RedisCacheKey.NOTICE, defaultConfiguration().entryTtl(Duration.ofSeconds(5L)));
    customConfigurationMap.put(RedisCacheKey.VIEWS, defaultConfiguration().entryTtl(Duration.ofDays(1L)));
    return customConfigurationMap;
  }
}
```

### 마무리

애플리케이션을 개발하다 보면 하나의 `cacheName`을 사용하는 게 아닌 여러 가지 `cacheName`를 추가하고, 설정을 각각 다르게 적용하고 싶을 수가 있다.
기존 `RedisCacheManager` 설정을 변경하면 `cacheName`마다 `Cache` 만료 시간을 다르게 설정할 수 있다.
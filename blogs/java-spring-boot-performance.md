---
id: java-spring-boot-performance
title: Java Spring Boot Performance Optimization
excerpt: Discover practical techniques to optimize your Spring Boot applications. From connection pooling to caching strategies, improve your application's performance.
date: 2025-01-08
author: Paramjit Saha
tags:
  - Java
  - Spring Boot
  - Performance
readTime: 10
---

# Java Spring Boot Performance Optimization

Performance is a feature. A slow API doesn't just frustrate users—it costs money in infrastructure, wastes human productivity, and can directly impact revenue. In this guide, we'll explore practical techniques to optimize your Spring Boot applications.

## Connection Pooling

The foundation of database performance is efficient connection management. Each database connection is expensive to create.

### Proper HikariCP Configuration

HikariCP is the default connection pool in Spring Boot and it's excellent. Here's optimal configuration:

```properties
# application.properties
spring.datasource.hikari.maximum-pool-size=20
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.idle-timeout=600000
spring.datasource.hikari.max-lifetime=1800000
```

**Key settings**:
- `maximum-pool-size`: 20 is good for most applications. Calculate based on: connections = ((core_count * 2) + disk_spindle_count)
- `minimum-idle`: Keep some connections warm during idle periods
- `connection-timeout`: Don't wait forever for a connection
- `max-lifetime`: Connections older than 30 minutes are cycled out

### Connection Pool Anti-Patterns

```java
// ❌ BAD: Creating a new connection for each request
public List<User> getUsers() {
    Connection conn = DriverManager.getConnection("jdbc:mysql://localhost/db");
    // ... query code
}

// ✅ GOOD: Use Spring Data JPA or connection pool
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByActive(boolean active);
}
```

Connection creation takes 5-10ms. If you do this 1000 times per second, that's 5-10 seconds wasted per second.

## Caching Strategies

### L1: In-Process Cache

The fastest cache is memory in your own application.

```java
@Service
public class ProductService {
    
    private final Map<Long, Product> cache = new ConcurrentHashMap<>();
    private final ProductRepository repository;
    
    @Cacheable("products")
    public Product getProduct(Long id) {
        return cache.computeIfAbsent(id, 
            key -> repository.findById(key).orElse(null)
        );
    }
    
    @CacheEvict("products", key = "#product.id")
    public void updateProduct(Product product) {
        repository.save(product);
    }
}
```

**When to use**: Data that rarely changes, accessed frequently
**Cost**: Memory usage, cache invalidation complexity

### L2: Redis Cache

For distributed systems, Redis is the go-to cache.

```java
@Configuration
@EnableCaching
public class CacheConfig {
    @Bean
    public CacheManager cacheManager(LettuceConnectionFactory factory) {
        return new RedisCacheManager.create(factory);
    }
}

@Service
public class OrderService {
    
    @Cacheable(value = "orders", key = "#orderId")
    public Order getOrder(Long orderId) {
        return repository.findById(orderId).orElse(null);
    }
    
    @CacheEvict(value = "orders", key = "#orderId")
    public void updateOrder(Long orderId, Order order) {
        repository.save(order);
    }
}
```

**TTL Strategy**:
```java
RedisCacheConfiguration config = RedisCacheConfiguration
    .defaultCacheConfig()
    .entryTtl(Duration.ofMinutes(10));
```

### Cache Invalidation Patterns

There are only two hard things in Computer Science: cache invalidation and naming things.

**1. Passive Expiration (TTL)**
- Set a time-to-live on cache entries
- Simple but can serve stale data

**2. Active Invalidation (Event-Driven)**
```java
@Component
public class OrderEventListener {
    @EventListener
    public void onOrderUpdated(OrderUpdatedEvent event) {
        cacheManager.getCache("orders").evict(event.getOrderId());
    }
}
```

**3. Cache-Aside Pattern**
- Check cache first
- If miss, load from DB and populate cache
- Best for read-heavy workloads

## Database Query Optimization

### N+1 Query Problem

```java
// ❌ BAD: N+1 queries
List<Order> orders = orderRepository.findAll();
for (Order order : orders) {
    List<Item> items = itemRepository.findByOrderId(order.getId()); 
    // This runs a query for each order!
}

// ✅ GOOD: Eager loading or join
@Query("SELECT o FROM Order o LEFT JOIN FETCH o.items")
List<Order> findAllWithItems();
```

Running 1000 orders causes 1001 queries. With proper joining, it's just 1 query.

### Query Indexing

```java
@Entity
@Table(name = "orders", indexes = {
    @Index(name = "idx_user_id", columnList = "user_id"),
    @Index(name = "idx_created_at", columnList = "created_at"),
    @Index(name = "idx_status", columnList = "status")
})
public class Order {
    @Id
    private Long id;
    
    @ManyToOne
    private User user;
    
    private LocalDateTime createdAt;
    private String status;
}
```

**Index rules**:
- Index columns used in WHERE clauses
- Index foreign keys
- Index columns used for sorting
- Don't over-index—writes become slower

### Pagination for Large Result Sets

```java
// ❌ BAD: Fetching all rows
List<Order> orders = orderRepository.findByStatus("pending");

// ✅ GOOD: Pagination
Page<Order> page = orderRepository.findByStatus(
    "pending", 
    PageRequest.of(0, 20, Sort.by("createdAt").descending())
);
```

Fetching 100k rows at once loads into memory and makes your response slow.

## Asynchronous Processing

Not everything needs a synchronous response.

```java
@Service
public class OrderService {
    
    @Async
    public CompletableFuture<EmailConfirmation> sendOrderEmail(Order order) {
        // This runs in a thread pool, doesn't block the request
        return emailService.send(order);
    }
    
    public Order createOrder(OrderRequest request) {
        Order order = repository.save(request.toEntity());
        sendOrderEmail(order); // Non-blocking
        return order;
    }
}
```

**Configure the thread pool**:
```properties
spring.task.execution.pool.core-size=10
spring.task.execution.pool.max-size=20
spring.task.execution.pool.queue-capacity=100
```

## Response Compression

Enable gzip compression in Spring Boot:

```properties
server.compression.enabled=true
server.compression.min-response-size=1024
```

This reduces JSON response sizes by 70-80%, especially for large result sets.

## Database Connection Timeout Tuning

```properties
# Connection acquisition timeout
spring.datasource.hikari.connection-timeout=5000

# Query timeout (database-specific)
spring.jpa.properties.hibernate.jdbc.fetch_size=50
spring.jpa.properties.hibernate.jdbc.batch_size=20
```

## Monitoring and Metrics

You can't optimize what you don't measure:

```java
@Service
public class UserService {
    
    private final MeterRegistry meterRegistry;
    
    @Timed(value = "user.fetch", description = "Time to fetch user")
    public User getUser(Long id) {
        // Your code here
    }
}
```

Monitor:
- Request latency (p50, p95, p99)
- Database query times
- Cache hit rates
- Thread pool queue depth
- JVM memory usage

## Performance Checklist

- [ ] Connection pool configured with appropriate size
- [ ] N+1 queries eliminated
- [ ] High-traffic queries have indexes
- [ ] Caching strategy implemented (Redis + local cache)
- [ ] Async processing for non-critical operations
- [ ] Response compression enabled
- [ ] Pagination implemented for list endpoints
- [ ] Monitoring and alerting in place
- [ ] Load tested before production
- [ ] Circuit breakers for external service calls

## Conclusion

Performance optimization is iterative. Measure, identify bottlenecks, optimize the worst offenders. Often, the biggest gains come from:

1. Fixing database queries (N+1, missing indexes)
2. Adding caching
3. Making I/O non-blocking

Start here, and you'll see 10x+ improvements.

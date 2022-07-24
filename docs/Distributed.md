## CAP&BASE

## Paxos 算法

## Leader选举

### Raft 算法

### Zab协议（ZooKeeper Atomic Broadcast 原子广播）

### 具体到组件

#### etcd

使用raft算法，比较currentTerm，三个状态Leader、Follower、Candidate

#### Redis 的 Sentinel 

使用 Raft 算法

#### kafka

#### zk

使用ZXID、myid来选举，ZAB协议，Leader、Follower，超过半数，选举（崩溃恢复）和同步数据（消息广播）

## 一致性

### 强一致性

### 弱一致性 

### 最终一致性 

## 分布式事务

## 分布式锁

场景：互联网秒杀，抢优惠卷，接口幂等性校验

### 基于数据库

### 基于Redis

**SET key value [EX seconds|PX milliseconds] [NX|XX] [KEEPTTL]**

```java
@RequestMapping(value = "/duduct_stock")
public String deductStock(){
    String lockKey = "product_001";

    try{
        Boolean result = stringRedisTemplate.opsForValue().setIfAbsent(lockKey,"wangcp",10,TimeUnit.SECONDS);
        if(!result){
            // 代表已经加锁了
            return "error_code";
        }
        // 从redis 中拿当前库存的值
        int stock = Integer.parseInt(stringRedisTemplate.opsForValue().get("stock"));
        if(stock > 0){
            int realStock = stock - 1;
            stringRedisTemplate.opsForValue().set("stock",realStock + "");
            System.out.println("扣减成功，剩余库存：" + realStock);
        }else{
            System.out.println("扣减失败，库存不足");
        }
    }finally {
        // 释放锁
        stringRedisTemplate.delete(lockKey);
    }

    return "end";
}
```

#### RedLock

Redis官方提出的一种分布式锁的算法

1. 顺序向n个节点请求加锁
2. 根据一定的超时时间来推断是不是跳过该节点
3. (n/2+1)节点加锁成功并且花费时间小于锁的有效期
4. 认定加锁成功

#### Redisson分布式锁

```text
@Bean
public RedissonClient redisson(){
    // 单机模式
    Config config = new Config();
    config.useSingleServer().setAddress("redis://192.168.3.170:6379").setDatabase(0);
    return Redisson.create(config);
}
```

```java
@RestController
public class IndexController {

    @Autowired
    private RedissonClient redisson;
    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    /**
     * 模拟下单减库存的场景
     * @return
     */
    @RequestMapping(value = "/duduct_stock")
    public String deductStock(){
        String lockKey = "product_001";
        // 1.获取锁对象
        RLock redissonLock = redisson.getLock(lockKey);
        try{
            // 2.加锁
            redissonLock.lock();  // 等价于 setIfAbsent(lockKey,"wangcp",10,TimeUnit.SECONDS);
            // 从redis 中拿当前库存的值
            int stock = Integer.parseInt(stringRedisTemplate.opsForValue().get("stock"));
            if(stock > 0){
                int realStock = stock - 1;
                stringRedisTemplate.opsForValue().set("stock",realStock + "");
                System.out.println("扣减成功，剩余库存：" + realStock);
            }else{
                System.out.println("扣减失败，库存不足");
            }
        }finally {
            // 3.释放锁
            redissonLock.unlock();
        }
        return "end";
    }
}
```

![Redisson 分布式锁实现原理图](https://pic3.zhimg.com/v2-9a9281a0be299b4fd960e7d0bbde10aa_b.jpg)

### 基于Zookeeper

## 分布式ID

## 高并发

### 消息队列

### 缓存

### 读写分离&分库分表

### 负载均衡

#### 一致性Hash

## 高可用

### 限流

### 降级

### 熔断

### 集群

## 高性能
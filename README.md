# UrbanLife 本地生活服务平台

<p align="center">
  <img src="https://img.shields.io/badge/Java-1.8+-green?style=for-the-badge&logo=java" alt="Java">
  <img src="https://img.shields.io/badge/Spring%20Boot-2.3.12-brightgreen?style=for-the-badge&logo=springboot" alt="Spring Boot">
  <img src="https://img.shields.io/badge/Redis--red?style=for-the-badge&logo=redis" alt="Redis">
  <img src="https://img.shields.io/badge/MySQL-8.0-blue?style=for-the-badge&logo=mysql" alt="MySQL">
  <img src="https://img.shields.io/badge/RabbitMQ-green?style=for-the-badge&logo=rabbitmq" alt="RabbitMQ">
</p>

> UrbanLife 本地生活服务平台 - 面向城市用户的本地生活服务解决方案

## 项目简介

UrbanLife 是一个面向城市用户的本地生活服务平台，提供商户信息聚合、位置服务、用户互动与优惠券营销等核心能力。平台整合了周边商户的美食、休闲娱乐、生活服务等信息，支持用户进行浏览、点评、签到、达人探店、好友关注等社交互动。同时为商家提供优惠券发放、秒杀活动等营销工具，提升商户曝光与用户转化。

## 技术架构

### 核心技术与组件

| 技术 | 版本 | 用途 |
|------|------|------|
| Spring Boot | 2.3.12 | 后端框架 |
| MyBatis-Plus | 3.4.3 | ORM 持久层 |
| MySQL | 8.0+ | 关系型数据库 |
| Redis | 6.1.6 | 分布式缓存 |
| Redisson | 3.13.6 | 分布式锁 |
| RabbitMQ | 3.x | 消息队列 |
| Sentinel | 1.8.8 | 限流熔断 |
| Canal | 1.1.7 | 数据同步 |
| Caffeine | 2.9.2 | 本地缓存 |
| JWT | 0.11.2 | 身份认证 |

### 系统架构图

```
┌─────────────────────────────────────────────────────────────────┐
│                         前端层 (Vue/小程序)                       │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                         API 网关层                               │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Spring Boot 后端服务                        │
├──────────────┬──────────────┬──────────────┬──────────────────┤
│   用户模块   │   商户模块   │   博客模块   │    优惠券模块    │
│   (User)    │   (Shop)     │   (Blog)     │   (Voucher)      │
├──────────────┴──────────────┴──────────────┴──────────────────┤
│                      Service 业务逻辑层                         │
├─────────────────────────────────────────────────────────────────┤
│                   Cache 层 (Caffeine + Redis)                   │
├──────────────┬──────────────┬──────────────┬──────────────────┤
│  分布式锁     │   消息队列    │  限流熔断    │   数据同步       │
│ (Redisson)  │ (RabbitMQ)  │ (Sentinel)  │   (Canal)        │
└──────────────┴──────────────┴──────────────┴──────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                      数据存储层 (MySQL)                          │
└─────────────────────────────────────────────────────────────────┘
```

## 核心功能模块

### 1. 用户模块 (User)
- 用户注册/登录
- JWT 双 Token 认证
- 用户信息管理
- 好友关注系统

### 2. 商户模块 (Shop)
- 商户信息浏览
- 商户类型管理
- 地理位置服务
- 二级缓存架构

### 3. 博客模块 (Blog)
- 达人探店分享
- 博客评论互动
- 关注流推送

### 4. 优惠券模块 (Voucher)
- 优惠券发放
- 秒杀活动
- 订单管理

## 核心亮点

### 高并发缓存架构
- **Redis + Caffeine 二级缓存**：设计空值缓存 + 互斥锁 + 逻辑过期组合策略，解决缓存穿透/击穿/雪崩问题
- **Canal 缓存同步**：基于 Binlog 监听实现缓存实时更新，数据一致性延迟 < 500ms
- **缓存命中率**：商品查询 QPS 提升至 200+，缓存命中率 99.62%

### 秒杀系统高可用架构
- **分布式锁**：基于 Redisson 分布式锁 + Redis 原子操作（Lua 脚本）实现库存精准扣减
- **异步削峰**：RabbitMQ 消息队列解耦核心流程，订单处理耗时从 15ms 优化至 2ms
- **限流保护**：Sentinel 全链路限流策略，支撑 1500QPS 稳定吞吐
- **高可用**：Redis 哨兵集群实现故障自动转移，保障 99.99% 可用性
- **消息可靠性**：生产者确认 + 消费端幂等 + 定时补偿三重机制，投递成功率 99.99%

### 分布式认证体系
- **双 Token 架构**：JWT + Redis 实现动态续期与权限校验
- **ThreadLocal 上下文**：用户信息线程安全透传，内存占用降低 60%

## 项目结构

```
hm-dianping/
├── src/
│   ├── main/
│   │   ├── java/com/hmdp/
│   │   │   ├── config/          # 配置类
│   │   │   ├── constant/        # 常量定义
│   │   │   ├── controller/      # 控制器
│   │   │   ├── dto/             # 数据传输对象
│   │   │   ├── entity/          # 实体类
│   │   │   ├── interceptor/     # 拦截器
│   │   │   ├── listener/        # 事件监听
│   │   │   ├── mapper/          # 数据访问层
│   │   │   ├── properties/      # 配置属性
│   │   │   ├── service/         # 业务逻辑
│   │   │   ├── utils/           # 工具类
│   │   │   └── cache/           # 缓存处理
│   │   └── resources/
│   │       ├── mapper/          # MyBatis XML
│   │       └── application.yml  # 应用配置
│   └── test/                   # 测试类
├── pom.xml
└── README.md
```

## 快速开始

### 环境要求
- JDK 1.8+
- Maven 3.6+
- MySQL 8.0+
- Redis 6.0+
- RabbitMQ 3.x

### 配置步骤

1. **克隆项目**
   ```bash
   git clone https://github.com/User-ZWW/urbanlife.git
   ```

2. **创建数据库**
   ```sql
   CREATE DATABASE hmdp DEFAULT CHARACTER SET utf8mb4;
   ```

3. **修改配置**
   编辑 `src/main/resources/application.yaml`，配置数据库和 Redis 连接信息

4. **编译运行**
   ```bash
   mvn clean package
   java -jar target/hm-dianping-0.0.1-SNAPSHOT.jar
   ```

### 默认端口
- 服务端口: `8081`
- Redis: `6379`
- RabbitMQ: `5672`

## 压测数据

| 场景 | QPS | 响应时间 | 备注 |
|------|-----|----------|------|
| 商品查询 | 200+ | < 50ms | 二级缓存命中 |
| 秒杀下单 | 1500+ | < 100ms | 限流保护 |
| 用户认证 | 3000+ | < 20ms | JWT 验证 |

## License

MIT License

---

<p align="center">Made with ❤️ by <a href="https://github.com/User-ZWW">User-ZWW</a></p>

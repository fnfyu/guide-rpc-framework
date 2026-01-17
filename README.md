# My-Simple-RPC：纯手工打造的高性能分布式 RPC 框架

## 🌟 项目简介

本项目是一个基于 **Netty 4.2** 开发的轻量级高性能远程过程调用（RPC）框架。
不同于臃肿的现成框架，本项目从零开始实现了从**动态代理**、**自定义二进制协议**到**服务注册发现**的全套核心逻辑，旨在深度还原分布式系统的底层通信原理。

---

## 🚀 核心特性

* **高效通信**：基于 Netty 非阻塞 I/O 模型，支持长连接复用，极大提升了吞吐量。
* **智能寻址**：集成 **Zookeeper** 作为注册中心，实现服务的自动注册与发现，彻底告别硬编码 IP。
* **负载均衡**：内置**随机**与**轮询**算法，支持服务端集群部署，实现请求的自动分摊。
* **私有协议**：自定义二进制传输协议（包含魔数、版本号、序列化类型、数据长度），有效解决网络**粘包/半包**问题。
* **极速序列化**：舍弃 Java 原生序列化，采用 **Kryo** 框架，数据包体积减小 80%，解析速度提升 5 倍以上。
* **异步调用**：底层使用 `CompletableFuture` 实现异步转同步，保证了调用方的透明体验。

---

## 🛠 技术栈

* **核心框架**：Java 17+
* **网络通信**：Netty 4.2
* **注册中心**：Apache Zookeeper (via Docker)
* **序列化**：Kryo 5.5
* **日志门面**：SLF4J + Slf4j-simple
* **项目管理**：Maven

---

## 📐 协议设计（自定义包格式）

```text
+----------+----------+----------------+------------+---------------+
|  Magic   |  Version | SerializerType |   Length   |      Data     |
| (4 bytes)| (1 byte) |    (1 byte)    | (4 bytes)  |   (N bytes)   |
+----------+----------+----------------+------------+---------------+

```

* **Magic (0xCAFEBABE)**：接头暗号，识别非法数据包。
* **Version**：协议版本，支持平滑升级。
* **SerializerType**：标识使用的序列化算法（如 Kryo）。

---

## 📖 快速上手

### 1. 环境准备

使用 Docker 启动 Zookeeper：

```bash
docker run -d --name rpc-zk -p 2181:2181 zookeeper

```

### 2. 定义接口

```java
public interface HelloService {
    String hello(String name);
}

```

### 3. 启动服务端

```java
// 实现并登记服务，支持多实例部署
ServiceRegistry registry = new ZkServiceRegistry();
NettyServer server = new NettyServer(12345);
registry.register(HelloService.class.getName(), new InetSocketAddress("127.0.0.1", 12345));
server.start();

```

### 4. 客户端调用

```java
ServiceRegistry registry = new ZkServiceRegistry();
RpcClientProxy proxy = new RpcClientProxy(registry);
HelloService service = proxy.getProxy(HelloService.class);

String result = service.hello("老人家"); // 像调用本地方法一样简单
System.out.println(result);

```

---

## 📈 演进历程

* [x] 第一阶段：Netty 基础打通，实现 Request/Response 交互。
* [x] 第二阶段：引入动态代理，实现调用细节的透明封装。
* [x] 第三阶段：接入 Zookeeper，实现动态服务发现与负载均衡。
* [x] 第四阶段：重写编解码层，实现私有协议与 Kryo 序列化。
* [ ] 第五阶段（计划）：接入 Spring Boot 注解驱动，实现 `@RpcReference` 自动注入。

---

**Author:** [fnfyu]
**Date:** 2026-01-17

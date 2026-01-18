My-Simple-RPC：工业级高性能分布式 RPC 框架 (V2.0)

🌟 项目简介



本项目已从 V1.0 的“原理原型”进化为 V2.0 工业级架构。在保持 Netty 4.2 高性能通信的基础上，深度集成了 Spring 容器与高可用治理方案，真正实现了“生产级”的容错与模块化设计。



🚀 V2.0 核心新增特性

全面 Spring 化 (Annotation-Driven)：引入 @RpcService 自定义注解，结合 Spring 的 BeanPostProcessor 实现服务的自动扫描、自动注册与自动注入。



长连接池化 (Channel Reuse)：打造 ChannelProvider 机制，实现 Netty 连接的缓存与复用，性能较短连接提升数倍。



高可用自愈 (Fault Tolerance)：



智能重试：在 Proxy 层实现“故障漂移”。当节点 A 崩溃或超时，自动重新寻址并切换至节点 B 重试。



双向心跳：集成 IdleStateHandler，5s 主动心跳维持，30s 被动断开清理，确保链路永远鲜活。



优雅停机 (Graceful Shutdown)：注册中心感知 Spring 容器销毁信号，关机自动注销 ZK 节点，拒绝“死地址”残留。



架构模块化 (Multi-Module)：彻底拆分为 rpc-common、rpc-server、rpc-client 三大模块，依赖清晰，解耦彻底。



📐 模块分工

rpc-common：传输协议定义、Kryo 序列化工厂、心跳协议包。



rpc-server：Spring 容器整合、服务自动注册逻辑、Netty 服务端引擎。



rpc-client：动态代理（含重试逻辑）、连接池管理、负载均衡算法。



🛠 技术栈更新

核心大脑：Spring Framework (IOC/DI)



网络通信：Netty 4.2 (长连接复用)



高可用保障：自动重试 + 心跳检测 + 优雅下线



其他：同 V1.0 (Java 17+, Zookeeper, Kryo)



📖 快速上手 (V2.0 注解版)

1\. 定义并开启服务



Java

@RpcService // 只要贴上这个标签，剩下的交给我

public class HelloServiceImpl implements HelloService {

&nbsp;   public String hello(String name) { return "你好, " + name; }

}

2\. 客户端透明调用 (带自动重试)



Java

// 代理层已内置 3 次重试逻辑

HelloService service = proxy.getProxy(HelloService.class);

String result = service.hello("老人家"); // 若节点 A 宕机，后台会自动切换到节点 B 成功返回

📈 演进历程 (新增)

第一至四阶段：(见 V1.0，完成 Netty 与 ZK 基础打通)



✅ 第五阶段：引入 Spring 容器化管理，实现注解驱动开发。



✅ 第六阶段：实现长连接池化与心跳监测，攻克“链路治理”难题。



✅ 第七阶段：代理层加入超时重试与故障漂移，实现系统“高可用”。



🚀 第八阶段 (计划)：支持配置文件 (properties/yaml) 动态调整参数。



Author: \[fnfyu]



Last Updated: 2026-01-18 (V2.0 Final)


# 概述
## [什么是服务总线消息传送？](service-bus-messaging-overview.md)
## [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
## [服务总线体系结构](service-bus-architecture.md)
## [常见问题](service-bus-faq.md)

# 入门
## [创建命名空间](service-bus-create-namespace-portal.md)
## 使用队列
### [.NET](service-bus-dotnet-get-started-with-queues.md)
### [Java](service-bus-java-how-to-use-queues.md)
### [Node.js](service-bus-nodejs-how-to-use-queues.md)
### [PHP](service-bus-php-how-to-use-queues.md)
### [Python](service-bus-python-how-to-use-queues.md)
### [Ruby](service-bus-ruby-how-to-use-queues.md)
### [REST](/rest/api/servicebus/queues)
## 使用主题和订阅
### [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
### [Java](service-bus-java-how-to-use-topics-subscriptions.md)
### [Node.js](service-bus-nodejs-how-to-use-topics-subscriptions.md)
### [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
### [Python](service-bus-python-how-to-use-topics-subscriptions.md)
### [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)
## [构建多层服务总线应用程序](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)

# 如何
## 规划和设计
### [托管服务标识（预览）](service-bus-managed-service-identity.md)
### [基于角色的访问控制（预览）](service-bus-role-based-access-control.md)
### [高级消息传送](service-bus-premium-messaging.md)
### [Azure 队列与服务总线队列的比较](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
### [优化性能](service-bus-performance-improvements.md)
### [异地灾难恢复和异地复制](service-bus-geo-dr.md)
### [异步消息传送和高可用性](service-bus-async-messaging.md)
### [处理中断和灾难问题](service-bus-outages-disasters.md)

## 开发
### 消息处理
#### [队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
#### [消息、有效负载和序列化](service-bus-messages-payloads.md)
#### [消息传输、锁定和处置](message-transfers-locks-settlement.md)
#### [消息序列化和时间戳](message-sequencing.md)
#### [消息到期时间（生存时间）](message-expiration.md)
### [身份验证和授权](service-bus-authentication-and-authorization.md)
#### [从 ACS 迁移到 SAS](service-bus-migrate-acs-sas.md)
#### [使用共享访问签名进行的身份验证](service-bus-sas.md)
### [主题筛选器和操作](topic-filters.md)
### [分区的队列和主题](service-bus-partitioning.md)
### [消息会话](message-sessions.md)
### AMQP
#### [AMQP 概述](service-bus-amqp-overview.md)
#### [.NET](service-bus-amqp-dotnet.md)
#### [Java](service-bus-amqp-java.md)
#### [Java 消息服务和 AMQP](service-bus-java-how-to-use-jms-api-amqp.md)
#### [AMQP 协议指南](service-bus-amqp-protocol-guide.md)
#### [基于 AMQP 请求-响应的操作](service-bus-amqp-request-response.md)
### 高级功能
#### [死信队列](service-bus-dead-letter-queues.md)
#### [预取消息](service-bus-prefetch.md)
#### [重复消息检测](duplicate-detection.md)
#### [消息计数器](message-counters.md)
#### [消息延迟](message-deferral.md)
#### [消息浏览](message-browsing.md)
#### [将实体与自动转发链接在一起](service-bus-auto-forwarding.md)
#### [事务处理](service-bus-transactions.md)
#### [配对命名空间实现](service-bus-paired-namespaces.md)
### [端到端跟踪和诊断](service-bus-end-to-end-tracing.md)
## 管理
### [使用 Azure 监视来监视服务总线](service-bus-metrics-azure-monitor.md)
### [服务总线管理库](service-bus-management-libraries.md)
### [诊断日志](service-bus-diagnostic-logs.md)
### [挂起和重新激活消息传递实体](entity-suspend.md)
### [使用 Azure 资源管理器模板](service-bus-resource-manager-overview.md)
#### [创建命名空间](service-bus-resource-manager-namespace.md)
#### [创建命名空间和队列](service-bus-resource-manager-namespace-queue.md)
#### [创建包含主题和订阅的命名空间](service-bus-resource-manager-namespace-topic.md)
#### [创建命名空间和队列的授权规则](service-bus-resource-manager-namespace-auth-rule.md)
#### [创建包含主题、订阅和规则的命名空间](service-bus-resource-manager-namespace-topic-with-rule.md)
#### 
### [使用 Azure PowerShell 预配实体](service-bus-manage-with-ps.md)

# 引用
## .NET
### [Microsoft.ServiceBus.Messaging (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging)
### [Microsoft.Azure.ServiceBus（.NET 标准）](/dotnet/api/microsoft.azure.servicebus)
## [Java](/java/api/overview/azure/servicebus)
## [Azure PowerShell](/powershell/module/azurerm.servicebus)
## [REST](/rest/api/servicebus)
## [异常](service-bus-messaging-exceptions.md)
## [配额](service-bus-quotas.md)
## [SQLFilter 语法](service-bus-messaging-sql-filter.md)
## [SQLRuleAction 语法](service-bus-messaging-sql-rule-action.md)

# 资源
## [Azure 路线图](https://azure.microsoft.com/roadmap/?category=enterprise-integration)
## [博客](https://blogs.msdn.microsoft.com/servicebus/)
## [MSDN 论坛](https://social.msdn.microsoft.com/forums/home?forum=servbus)
## [定价](https://azure.microsoft.com/pricing/details/service-bus/)
## [定价计算器](https://azure.microsoft.com/pricing/calculator/)
## [定价详细信息](service-bus-pricing-billing.md)
## [示例](service-bus-samples.md)
## [ServiceBus360](https://www.servicebus360.com/)
## [服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer)
## [服务更新](https://azure.microsoft.com/updates/?product=service-bus)
## [堆栈溢出](http://stackoverflow.com/questions/tagged/azureservicebus)
## [视频](https://azure.microsoft.com/documentation/videos/index/?services=service-bus)



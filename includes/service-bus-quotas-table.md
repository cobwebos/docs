下表列出了特定于服务总线消息传送配额信息。 有关定价的信息和其他服务总线配额，请参阅[Service Bus 定价](https://azure.microsoft.com/pricing/details/service-bus/)概述。

| 配额名称 | 作用域 | 类型 | 超出时的行为 | 值 |
| --- | --- | --- | --- | --- |
| 每个 Azure 订阅的基本 / 标准命名空间的最大数目 |Namespace |静态 |门户将拒绝更多的基本 / 标准命名空间的后续请求。 |100|
| 每个 Azure 订阅的高级命名空间的最大数目 |Namespace |静态 |其他高级命名空间的后续请求将拒绝的门户。 |10 |
| 队列/主题大小 |实体 |创建队列/主题时定义。 |将拒绝传入消息，且调用代码将收到异常。 |1、 2、 3、 4 或 5 GB。<br /><br />如果[分区](../articles/service-bus-messaging/service-bus-partitioning.md)是启用，最大队列/主题大小是 80 GB。 |
| 命名空间上的并发连接数 |Namespace |静态 |将拒绝后续的附加连接请求，且调用代码将收到异常。 REST 操作不计入并发 TCP 连接。 |NetMessaging: 1000<br /><br />AMQP: 5000 |
| 并发接收队列/主题/订阅实体上的请求数 |实体 |静态 |后续的接收请求将被拒绝，并且调用代码将收到异常。 此配额适用于一个主题上的所有订阅都接收操作的并发的总数。 |5,000 |
| 每个服务命名空间的主题/队列数 |系统级 |静态 |创建新主题或队列服务命名空间上的后续请求将被拒绝。 因此，如果是通过配置[Azure 门户][Azure portal]，将生成错误消息。 如果从管理 API 调用，通过调用代码将收到异常。 |10,000<br /><br />主题和队列服务命名空间中的总数必须小于或等于 10000。<br/>这不是适用于高级因为分区的所有实体。 |
| 每个服务命名空间的分区主题/队列数 |系统级 |静态 |创建新分区的主题或队列服务命名空间上的后续请求将被拒绝。 因此，如果是通过配置[Azure 门户][Azure portal]，将生成错误消息。 如果从管理 API，调用**QuotaExceededException**调用代码将收到异常。 |基本和标准层-100<br />[高级](../articles/service-bus-messaging/service-bus-premium-messaging.md)-1000 （每个消息传送单元）<br/><br />每个分区的队列或主题将计入每个命名空间的 10,000 个实体的配额。 |
| 任何消息传递实体路径的最大大小： 队列或主题 |实体 |静态 |- |260 个字符 |
| 任一消息实体名称的最大大小： 命名空间、 订阅或订阅的规则 |实体 |静态 |- |50 个字符 |
| 队列/主题/订阅实体的消息大小 |系统级 |静态 |将拒绝超过这些配额的传入消息，且调用代码将收到异常。 |最大消息大小： 256 KB ([标准层](../articles/service-bus-messaging/service-bus-premium-messaging.md)) / 1 MB ([高级层](../articles/service-bus-messaging/service-bus-premium-messaging.md))。 <br /><br />**请注意**由于系统开销，此限制通常是稍微降低。<br /><br />最大标题大小： 64 KB<br /><br />属性包中的标头属性的最大数目：**字节/int。MaxValue**<br /><br />属性包中的最大大小： 没有明确的限制。 受最大标头大小限制。 |
| 队列/主题/订阅实体的消息属性大小 |系统级 |静态 |A **SerializationException**则会生成异常。 |每个属性的最大消息属性大小为 32k。 所有属性的累计大小不得超过 64k。 这适用于的整个标头[BrokeredMessage](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.aspx)，其中包含用户属性，以及系统属性 (如[SequenceNumber](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.sequencenumber.aspx)，[标签](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.label.aspx)， [MessageId](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx)，依次类推)。 |
| 每个主题的订阅数 |系统级 |静态 |将拒绝为主题创建附加订阅的后续请求。 因此，如果通过门户进行配置，则将显示一条错误消息。 如果从管理 API 调用通过调用代码将收到异常。 |2,000 |
| 每个主题的 SQL 筛选器的数量 |系统级 |静态 |将拒绝创建更多筛选器主题的后续请求，且调用代码将收到异常。 |2,000 |
| 每个主题的相关性筛选器数 |系统级 |静态 |将拒绝创建更多筛选器主题的后续请求，且调用代码将收到异常。 |100,000 |
| SQL 筛选器/操作的大小 |系统级 |静态 |将拒绝创建更多筛选器的后续请求，且调用代码将收到异常。 |筛选器条件字符串的最大长度： 1024 (1 K)。<br /><br />规则操作字符串的最大长度： 1024 (1 K)。<br /><br />每个规则操作的表达式的最大数目： 32。 |
| 数[SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)每个命名空间、 队列或主题的规则 |实体，命名空间 |静态 |将拒绝其他规则创建的后续请求，且调用代码将收到异常。 |最大规则数： 12。 <br /><br /> 在 Service Bus 命名空间配置的规则适用于所有队列和该命名空间中的主题。 |

[Azure portal]: https://portal.azure.com
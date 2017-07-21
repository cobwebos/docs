下表列出了特定于 Azure 事件中心的配额和限制。 有关事件中心定价的信息，请参阅[事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。

| 限制 | 范围 | 类型 | 超出时的行为 | 值 |
| --- | --- | --- | --- | --- |
| 每个命名空间的事件中心数 |命名空间 |静态 |创建新的命名空间的后续请求将被拒绝。 |10 |
| 每个事件中心的分区数 |实体 |静态 |- |32 |
| 每个事件中心的使用者组数 |实体 |静态 |- |20 |
| 每个命名空间的 AMQP 连接数 |命名空间 |静态 |系统将拒绝后续的附加连接请求，且调用代码会收到异常。 |5,000 |
| 事件中心事件的最大大小|系统范围 |静态 |- |256 KB |
| 事件中心名称的最大大小 |实体 |静态 |- |50 个字符 |
| 每个使用者组的非 epoch 接收者数 |实体 |静态 |- |5 |
| 事件数据的最长保留期限 |实体 |静态 |- |1-7 天 |
| 最大吞吐量单位 |命名空间 |静态 |超出吞吐量单位限制会导致数据受到限制，并生成 [ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)。 可以通过填写[支持请求](/azure/azure-supportability/how-to-create-azure-support-request)为标准层请求更多的吞吐量单位。 [额外的吞吐量单位](../articles/event-hubs/event-hubs-auto-inflate.md)将基于承诺的购买以大小为 20 个单位的块的形式提供。 |20 |


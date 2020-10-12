---
title: 排查适用于 Apache Kafka 的 Azure 事件中心的问题
description: 本文介绍如何排查适用于 Apache Kafka 的 Azure 事件中心的问题
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e32e02947b9f004755381d562fd3f3c897b70674
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90061421"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>针对事件中心的 Apache Kafka 故障排除指南
本文提供的故障排除技巧适用于你在使用适用于 Apache Kafka 的事件中心时可能会遇到的问题。 

## <a name="server-busy-exception"></a>服务器繁忙异常
由于 Kafka 限制，你可能会收到“服务器忙”异常。 使用 AMQP 客户端时，事件中心会在遇到服务限制时立即返回“服务器忙”异常。 它相当于“稍后重试”消息。 在 Kafka 中，消息在完成前会存在延迟。 将在生成/提取响应中以 `throttle_time_ms` 形式返回延迟时间长度（以毫秒为单位）。 大多数情况下，系统不会在事件中心仪表板上将这些延迟的请求记录为“服务器忙”异常， 而是使用响应的 `throttle_time_ms` 值来指示吞吐量已超出预配的配额。

如果流量过多，服务会出现以下行为：

- 如果生成请求的延迟超过请求超时，事件中心会返回“策略冲突”错误代码。
- 如果提取请求的延迟超过请求超时，事件中心会将请求记录为“受限制”并以空记录集进行响应，不返回错误代码。

[专用群集](event-hubs-dedicated-overview.md)没有限制机制。 你可以自由地使用所有群集资源。

## <a name="no-records-received"></a>未收到记录
你可能会看到，使用者未收到任何记录，并且会不断地重新进行平衡。 在这种情况下，使用者不会获得任何记录，只会不断地重新进行平衡。 它发生时没有异常或错误，但 Kafka 日志会显示使用者在尝试重新加入组和分配分区时处于停滞状态。 此问题有多种可能的原因：

- 确保 `request.timeout.ms` 至少为建议值 60000，`session.timeout.ms` 至少为建议值 30000。 这些设置过低可能会导致使用者超时，超时会导致重新平衡（重新平衡又会导致超时，而超时又会导致重新平衡，陷入一个死循环） 
- 如果你的配置与这些建议的值相符，而你仍然看到系统在不断地进行重新平衡，则请提交问题（请确保在问题中提供完整的配置，方便我们进行调试）！

## <a name="compressionmessage-format-version-issue"></a>压缩/消息格式版本问题
Kafka 支持压缩，而用于 Kafka 的事件中心目前不支持压缩。 当客户端尝试向我们的中转站发送压缩的 Kafka 消息时，会导致错误，这些错误会提到消息格式版本（例如，`The message format version on the broker does not support the request.`）。

如果必须使用压缩的数据，则请在将数据发送到中转站之前先压缩数据，在收到数据后再将其解压缩，这是一种有效的解决方法。 消息正文只是发送给服务的字节数组，因此客户端压缩/解压缩不会导致任何问题。

## <a name="unknownserverexception"></a>UnknownServerException
你可能会收到一个 Kafka 客户端库中的 UnknownServerException，类似于以下示例： 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

通过 Microsoft 支持部门开具一个票证。  在调试问题时，调试级别的日志记录和异常时间戳 (UTC) 很有用。 

## <a name="other-issues"></a>其他问题
如果在事件中心使用 Kafka 时遇到问题，请检查以下项。

- **防火墙阻止流量** - 确保防火墙未阻止端口 **9093**。
- **TopicAuthorizationException** - 此异常的最常见原因包括：
    - 配置文件中的连接字符串中有拼写错误，或者
    - 尝试在“基本”层命名空间中使用用于 Kafka 的事件中心。 [仅标准和专用层命名空间支持](https://azure.microsoft.com/pricing/details/event-hubs/)Kafka 功能的事件中心。
- **Kafka 版本不匹配** - 用于 Kafka 生态系统的事件中心支持 Kafka 1.0 及更高版本。 某些使用 Kafka 0.10 及更高版本的应用程序有时可能会有效，因为 Kafka 协议具有后向兼容性，但我们强烈建议你不要使用旧的 API 版本。 Kafka 0.9 及更低版本不支持必需的 SASL 协议，因此无法连接到事件中心。
- **与 Kafka 配合使用时，AMQP 标头上出现奇怪的编码** - 通过 AMQP 将事件发送到事件中心时，系统会采用 AMQP 编码来序列化任何 AMQP 有效负载标头。 Kafka 使用者不会反序列化 AMQP 中的标头。 若要读取标头值，请手动解码 AMQP 标头。 或者，如果你知道要通过 Kafka 协议来使用 AMQP 标头，则可避免使用这些标头。 有关详细信息，请参阅[此 GitHub 问题](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56)。
- **SASL 身份验证** - 将框架与事件中心所需的 SASL 身份验证协议配合使用可能不是看起来那么容易。 看看你是否可以在 SASL 身份验证的基础上使用框架的资源来排查配置问题。 

## <a name="limits"></a>限制
我们可以将 Apache Kafka 与事件中心 Kafka 进行比较。 大多数情况下，Azure 事件中心的 Kafka 接口具有相同的默认值、属性、错误代码和 Apache Kafka 执行的常规行为。 下面列出了这二者明显不同的情况（或事件中心施加了某个限制而 Kafka 没有施加该限制的情况）：

- `group.id` 属性的最大长度为 256 个字符
- `offset.metadata.max.bytes` 的最大大小为 1024 个字节
- 每个分区的偏移量提交数限制为4次，最大内部日志大小为 1 MB


## <a name="next-steps"></a>后续步骤
若要详细了解事件中心和适用于 Kafka 的事件中心，请参阅以下文章：  

- [针对事件中心的 Apache Kafka 开发人员指南](apache-kafka-developer-guide.md)
- [针对事件中心的 Apache Kafka 迁移指南](apache-kafka-migration-guide.md)
- [常见问题解答 - 用于 Apache Kafka 的事件中心](apache-kafka-frequently-asked-questions.md)
- [建议的配置](apache-kafka-configurations.md)

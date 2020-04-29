---
title: 排查 Apache Kafka 的 Azure 事件中心的问题
description: 本文介绍如何解决 Apache Kafka 的 Azure 事件中心的问题
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 12ddc5fa74b7a1b42bbd64fde9ec3410b1c1e425
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606725"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Apache Kafka 事件中心的故障排除指南
本文提供了有关使用事件中心进行 Apache Kafka 时可能遇到的问题的故障排除提示。 

## <a name="server-busy-exception"></a>服务器忙异常
由于 Kafka 限制，你可能会收到 "服务器忙" 异常。 使用 AMQP 客户端时，事件中心会在服务中止时立即返回**服务器忙**例外。 它相当于 "稍后重试" 消息。 在 Kafka 中，消息在完成前将被延迟。 以毫秒为单位返回延迟长度， `throttle_time_ms`如生成/提取响应中所示。 在大多数情况下，这些延迟的请求不会在事件中心仪表板上记录为服务器繁忙异常。 相反，应将响应`throttle_time_ms`的值用作吞吐量超出预配配额的指标。

如果流量过多，服务具有以下行为：

- 如果生成请求的延迟超过请求超时，事件中心将返回**策略冲突**错误代码。
- 如果提取请求的延迟超过请求超时，事件中心会将请求记录为 "已限制" 并响应空记录集，无错误代码。

[专用群集](event-hubs-dedicated-overview.md)没有限制机制。 你可以自由地使用所有群集资源。

## <a name="no-records-received"></a>未接收记录
你可能会看到使用者未获得任何记录和不断重新平衡。 在这种情况下，使用者不会获得任何记录和不断重新平衡。 发生异常或错误时，Kafka 日志会显示使用者停滞尝试重新加入组并分配分区。 有几个可能的原因：

- 请确保至少`request.timeout.ms`为建议值60000，并且至少`session.timeout.ms`为建议值30000。 这些设置过低可能会导致使用者超时，导致间重新平衡（这会导致更多的超时，从而导致重新平衡等） 
- 如果你的配置与这些建议的值相匹配，并且仍会看到持续重新平衡，则可以自由地解决问题（请确保在此问题中包含整个配置，以便我们可以帮助调试）！

## <a name="compressionmessage-format-version-issue"></a>压缩/消息格式版本问题
Kafka 支持压缩，当前 Kafka 的事件中心不支持。 当客户端尝试向代理发送压缩的 Kafka 消息时`The message format version on the broker does not support the request.`，会导致提到消息格式版本（例如）的错误。

如果需要压缩的数据，请在将数据发送到代理并在接收后解压缩之前压缩数据，这是一个有效的解决方法。 消息正文只是服务的字节数组，因此客户端压缩/解压缩不会导致任何问题。

## <a name="unknownserverexception"></a>UnknownServerException
你可能会收到类似于以下示例的 Kafka 客户端库中的 UnknownServerException： 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

使用 Microsoft 支持人员打开票证。  在调试问题时，UTC 中的调试级别日志记录和异常时间戳非常有用。 

## <a name="other-issues"></a>其他问题
如果在事件中心上使用 Kafka 时遇到问题，请检查以下各项。

- **防火墙阻止流量**-请确保防火墙未阻止端口**9093** 。
- **TopicAuthorizationException** -此异常的最常见原因是：
    - 配置文件中的连接字符串中有拼写错误，或
    - 尝试将事件中心用于基本层命名空间上的 Kafka。 [仅标准和专用层命名空间支持](https://azure.microsoft.com/pricing/details/event-hubs/)Kafka 的事件中心。
- **Kafka 版本不匹配**-Kafka 生态系统的事件中心支持 Kafka 版本1.0 及更高版本。 某些使用 Kafka 版本0.10 和更高版本的应用程序有时可能会因为 Kafka 协议的向后兼容性而有效，但我们强烈建议不要使用旧的 API 版本。 Kafka 版本0.9 及更早版本不支持必需的 SASL 协议，无法连接到事件中心。
- **使用 Kafka 时，对 AMQP 标头使用奇怪的编码**-在通过 AMQP 将事件发送到事件中心时，任何 AMQP 负载标头都将按 AMQP 编码进行序列化。 Kafka 使用者不会从 AMQP 反序列化标头。 若要读取标头值，请手动解码 AMQP 标头。 或者，如果知道要通过 Kafka 协议使用 AMQP 标头，则可以避免使用这些标头。 有关详细信息，请参阅[此 GitHub 问题](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56)。
- **SASL 身份验证**-使框架与事件中心所需的 SASL 身份验证协议共同合作，这种方法比眼睛更难。 查看是否可以在 SASL 身份验证上使用框架的资源对配置进行故障排除。 

## <a name="limits"></a>限制
Apache Kafka 与事件中心 Kafka。 大多数情况下，Kafka 生态系统的事件中心具有相同的默认值、属性、错误代码和 Apache Kafka 执行的常规行为。 下面列出了这两种显式不同的实例（或事件中心施加了 Kafka 不限制的情况）：

- `group.id`属性的最大长度为256个字符
- 的最大大小`offset.metadata.max.bytes`为1024个字节
- 每个分区的偏移提交数限制为每个分区4个调用/秒，最大内部日志大小为 1 MB


## <a name="next-steps"></a>后续步骤
若要详细了解事件中心和适用于 Kafka 的事件中心，请参阅以下文章：  

- [事件中心 Apache Kafka 开发人员指南](apache-kafka-developer-guide.md)
- [事件中心 Apache Kafka 迁移指南](apache-kafka-migration-guide.md)
- [常见问题-事件中心 Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [建议的配置](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

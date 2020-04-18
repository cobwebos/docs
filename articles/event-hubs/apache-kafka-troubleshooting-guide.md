---
title: 阿帕奇卡夫卡 Azure 事件中心的问题排除问题
description: 本文演示如何解决 Apache Kafka 的 Azure 事件中心的问题
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
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606725"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>事件中心的阿帕奇卡夫卡故障排除指南
本文提供有关在使用 Apache Kafka 事件中心时可能会遇到的问题的疑难解答提示。 

## <a name="server-busy-exception"></a>服务器忙异常
由于 Kafka 限制，您可能会收到服务器忙异常。 对于 AMQP 客户端，事件中心在服务限制时立即返回**服务器忙**异常。 它等效于"稍后再试"消息。 在卡夫卡，消息在完成之前会延迟。 延迟长度返回以毫秒为单位，如`throttle_time_ms`在生产/提取响应中。 在大多数情况下，这些延迟请求不会记录为事件中心仪表板上的 ServerBusy 异常。 相反，响应`throttle_time_ms`的值应用作吞吐量超过预配配额的指示器。

如果流量过大，则服务具有以下行为：

- 如果生成请求的延迟超过请求超时，事件中心将返回**策略冲突**错误代码。
- 如果提取请求的延迟超过请求超时，事件中心将请求记录为受限，并响应空记录集和无错误代码。

[专用群集](event-hubs-dedicated-overview.md)没有限制机制。 您可以自由使用所有群集资源。

## <a name="no-records-received"></a>未收到任何记录
您可能会看到消费者没有得到任何记录，并不断重新平衡。 在这种情况下，消费者不会获得任何记录并不断重新平衡。 发生异常或错误时，没有异常或错误，但 Kafka 日志将显示使用者在尝试重新加入组并分配分区时遇到卡住。 有几个可能的原因：

- 请确保您的推荐值`request.timeout.ms`至少为 60000，并且您的`session.timeout.ms`建议值至少为 30000。 这些设置过低可能会导致使用者超时，进而导致重新平衡（进而导致更多超时，从而导致更多重新平衡，等等） 
- 如果您的配置与这些建议值匹配，并且您仍然看到不断的重新平衡，请随时打开问题（请确保在问题中包括您的整个配置，以便我们可以帮助调试）！

## <a name="compressionmessage-format-version-issue"></a>压缩/消息格式版本问题
卡夫卡支持压缩，卡夫卡的活动中心目前不支持压缩。 当客户端尝试向我们的经纪人发送压缩 Kafka`The message format version on the broker does not support the request.`消息时，将会导致提及消息格式版本（例如），错误。

如果需要压缩数据，在将数据发送到代理之前压缩数据并在接收后进行解压缩是一种有效的解决方法。 消息正文只是服务的字节数组，因此客户端压缩/解压缩不会导致任何问题。

## <a name="unknownserverexception"></a>未知服务器例外
您可能会收到来自 Kafka 客户端库的未知服务器例外，类似于以下示例： 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

在 Microsoft 支持下打开票证。  UTC 中的调试级日志记录和异常时间戳有助于调试问题。 

## <a name="other-issues"></a>其他问题
如果您在事件中心上使用 Kafka 时看到问题，请检查以下项目。

- **防火墙阻止流量**- 确保端口**9093**未被防火墙阻止。
- **主题授权异常**- 此异常的最常见原因是：
    - 配置文件中连接字符串中的拼写错误，或
    - 尝试在基本层命名空间上使用 Kafka 的事件中心。 Kafka 的事件中心[仅支持标准和专用层命名空间](https://azure.microsoft.com/pricing/details/event-hubs/)。
- **卡夫卡版本不匹配**- 卡夫卡生态系统的事件中心支持卡夫卡版本 1.0 及更高版本。 由于 Kafka 协议的向后兼容性，某些使用 Kafka 版本 0.10 和更高版本的应用程序有时可能工作，但我们强烈建议不要使用旧的 API 版本。 Kafka 版本 0.9 和更早版本不支持所需的 SASL 协议，并且无法连接到事件中心。
- **使用 Kafka 时，AMQP 标头上的奇怪编码**- 通过 AMQP 将事件发送到事件中心时，任何 AMQP 有效负载标头都将在 AMQP 编码中序列化。 卡夫卡使用者不会从 AMQP 中解序列化标头。 要读取标头值，请手动解码 AMQP 标头。 或者，如果您知道将通过 Kafka 协议使用 AMQP 标头，则可以避免使用 AMQP 标头。 有关详细信息，请参阅此[GitHub 问题](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56)。
- **SASL 身份验证**- 让框架与事件中心所需的 SASL 身份验证协议协作可能比满足眼睛更难。 查看是否可以使用 SASL 身份验证上的框架资源对配置进行故障排除。 

## <a name="limits"></a>限制
阿帕奇卡夫卡 vs. 事件中心卡夫卡。 在大多数情况下，卡夫卡生态系统的事件中心与 Apache Kafka 具有相同的默认值、属性、错误代码和常规行为。 下面列出了这两个显式不同的实例（或事件中心施加 Kafka 未施加的限制的情况）：

- `group.id`属性的最大长度为 256 个字符
- 的最大大小`offset.metadata.max.bytes`为 1024 字节
- 偏移提交在每次分区 4 次调用/秒时受到限制，最大内部日志大小为 1 MB


## <a name="next-steps"></a>后续步骤
若要详细了解事件中心和适用于 Kafka 的事件中心，请参阅以下文章：  

- [阿帕奇卡夫卡活动中心开发人员指南](apache-kafka-developer-guide.md)
- [事件中心的阿帕奇卡夫卡迁移指南](apache-kafka-migration-guide.md)
- [常见问题 - 阿帕奇卡夫卡事件中心](apache-kafka-frequently-asked-questions.md)
- [推荐的配置](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

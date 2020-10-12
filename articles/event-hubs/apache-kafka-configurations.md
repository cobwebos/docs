---
title: 建议用于 Apache Kafka 客户端的配置 - Azure 事件中心
description: 本文提供了建议用于与 Apache Kafka 的 Azure 事件中心进行交互的客户端的 Apache Kafka 配置。
ms.topic: reference
ms.date: 07/20/2020
ms.openlocfilehash: f9a03d1d3433461a575b32cd69893408a8b0ef97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095876"
---
# <a name="recommended-configurations-for-apache-kafka-clients"></a>建议用于 Apache Kafka 客户端的配置
下面是从 Apache Kafka 客户端应用程序使用 Azure 事件中心时的建议配置。 

## <a name="java-client-configuration-properties"></a>Java 客户端配置属性

### <a name="producer-and-consumer-configurations"></a>生成者和使用者配置

properties | 建议的值 | 允许的范围 | 备注
---|---:|-----:|---
`metadata.max.age.ms` | 180000（近似值） | < 240000 | 可以降低，以便更快地获取元数据更改。
`connections.max.idle.ms`   | 180000 | < 240000 | Azure 会关闭空闲时间 > 240000 毫秒的入站 TCP，这可能会导致在连接已“死”的情况下继续发送（因为发送超时而显示为过期的批）。

### <a name="producer-configurations-only"></a>仅限生成者配置
可在[此处](https://kafka.apache.org/documentation/#producerconfigs)找到生成者配置。

properties | 建议的值 | 允许的范围 | 备注
---|---:|---:|---
`max.request.size` | 1000000 | < 1046528 | 如果发送的请求大于 1,046,528 字节，则服务会关闭连接。  此值**必须**更改，否则会导致高吞吐量生成场景中出现问题。
`retries` | > 0 | | 可能需要增大 delivery.timeout.ms 值，请参阅文档。
`request.timeout.ms` | 30000 .. 60000 | > 20000| EH 会在内部默认设置为最小值，即 20,000 毫秒。  虽然系统会接受超时值较低的请求，但不保证客户端行为。
`metadata.max.idle.ms` | 180000 | > 5000 | 控制生成者为空闲的主题缓存元数据的时间长度。 如果自上次生成主题以来过去的时间超过了元数据空闲持续时间，系统会忘记该主题的元数据，在下一次访问它时会强制执行元数据提取请求。
`linger.ms` | > 0 | | 对于高吞吐量场景，逗留值应该等于最大的可容忍值，以便利用批处理。
`delivery.timeout.ms` | | | 请根据公式 (`request.timeout.ms` + `linger.ms`) * `retries` 进行设置。
`enable.idempotence` | false | | 目前不支持幂等。
`compression.type` | `none` | | 目前不支持压缩。

### <a name="consumer-configurations-only"></a>仅限使用者配置
可在[此处](https://kafka.apache.org/documentation/#consumerconfigs)找到使用者配置。

properties | 建议的值 | 允许的范围 | 备注
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 | | 3000 为默认值，不应更改。
`session.timeout.ms` | 30000 |6000 .. 300000| 从 30000 开始。如果由于检测信号丢失而出现频繁地进行重新平衡的情况，请增大此值。


## <a name="librdkafka-configuration-properties"></a>librdkafka 配置属性
主 `librdkafka` 配置文件（[链接](https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md)）包含以下属性的扩展说明。

### <a name="producer-and-consumer-configurations"></a>生成者和使用者配置

properties | 建议的值 | 允许的范围 | 备注
---|---:|-----:|---
`socket.keepalive.enable` | 是 | | 如果预计连接会空闲，则此项为必需项。  Azure 会关闭空闲时间 > 240,000 毫秒的入站 TCP。
`metadata.max.age.ms` | ~ 180000| < 240000 | 可以降低，以便更快地获取元数据更改。

### <a name="producer-configurations-only"></a>仅限生成者配置

properties | 建议的值 | 允许的范围 | 备注
---|---:|-----:|---
`retries` | > 0 | | 默认值为 2。 建议保留此值。 
`request.timeout.ms` | 30000 .. 60000 | > 20000| EH 会在内部默认设置为最小值，即 20,000 毫秒。  `librdkafka` 默认值为 5000，这可能会产生问题。 虽然系统会接受超时值较低的请求，但不保证客户端行为。
`partitioner` | `consistent_random` | 请参阅 librdkafka 文档 | `consistent_random` 是默认值，且为最佳值。  大多数情况下，可以理想地处理空键和 null 键。
`enable.idempotence` | false | | 目前不支持幂等。
`compression.codec` | `none` || 目前不支持压缩。

### <a name="consumer-configurations-only"></a>仅限使用者配置

properties | 建议的值 | 允许的范围 | 备注
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 || 3000 为默认值，不应更改。
`session.timeout.ms` | 30000 |6000 .. 300000| 从 30000 开始。如果由于检测信号丢失而出现频繁地进行重新平衡的情况，请增大此值。


## <a name="further-notes"></a>更详尽的说明

请查看下表中与配置相关的常见错误情况。

症状 | 问题 | 解决方案
----|---|-----
由于重新平衡而导致偏移量提交失败 | 使用者在两次调用 poll() 之间等待的时间太长，因此服务将该使用者踢出了群组。 | 有几种选项： <ul><li>增大会话超时</li><li>减小消息批大小以提高处理速度</li><li>改进处理并行性，以避免阻塞 consumer.poll()</li></ul> 应用这三者的某个组合可能是最明智的。
生成吞吐量高时的网络异常 | 你是否正在使用 Java 客户端 + 默认的 max.request.size？  你的请求可能太大。 | 请参阅上述 Java 配置。

## <a name="next-steps"></a>后续步骤
有关所有 Azure 服务的配额和限制，请参阅 [Azure 订阅和服务限制、配额和约束](..//azure-resource-manager/management/azure-subscription-service-limits.md)。 

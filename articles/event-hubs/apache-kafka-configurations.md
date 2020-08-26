---
title: Apache Kafka 客户端的推荐配置-Azure 事件中心
description: 本文针对与 Azure 事件中心进行 Apache Kafka 交互的客户端提供了建议的 Apache Kafka 配置。
ms.topic: reference
ms.date: 07/20/2020
ms.openlocfilehash: f9a03d1d3433461a575b32cd69893408a8b0ef97
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095876"
---
# <a name="recommended-configurations-for-apache-kafka-clients"></a>Apache Kafka 客户端的推荐配置
下面是有关从 Apache Kafka 客户端应用程序使用 Azure 事件中心的推荐配置。 

## <a name="java-client-configuration-properties"></a>Java 客户端配置属性

### <a name="producer-and-consumer-configurations"></a>制造者和使用者配置

properties | 建议值 | 允许的范围 | 注释
---|---:|-----:|---
`metadata.max.age.ms` | 180000（近似） | < 240000 | 可以降低，以便更快地获取元数据更改。
`connections.max.idle.ms`   | 180000 | < 240000 | Azure 会关闭入站 TCP 空闲 > 240000 ms，这可能会导致发送死连接（因为发送超时而显示为过期的批）。

### <a name="producer-configurations-only"></a>仅限生成方配置
可在[此处](https://kafka.apache.org/documentation/#producerconfigs)找到生成者配置。

properties | 建议值 | 允许的范围 | 注释
---|---:|---:|---
`max.request.size` | 1000000 | < 1046528 | 如果发送的请求大于1046528字节，则该服务将关闭连接。  *此值**必须**更改，并会导致高吞吐量产生的问题。*
`retries` | > 0 | | 可能需要增加 delivery.timeout.ms 值，请参阅文档。
`request.timeout.ms` | 30000.。 60000 | > 20000| EH 在内部默认为最小为20000毫秒。  *尽管接受较低超时值的请求，但不保证客户端行为。*
`metadata.max.idle.ms` | 180000 | > 5000 | 控制制造者为空闲主题缓存元数据的时间长度。 如果自上次生成主题以来经过的时间超过了元数据空闲时间，则会忘记该主题的元数据，并且对它的下一次访问将强制执行元数据提取请求。
`linger.ms` | > 0 | | 对于高吞吐量方案，逗留值应该等于最大的可容忍值才能利用批处理。
`delivery.timeout.ms` | | | 根据公式（ `request.timeout.ms`  +  `linger.ms` ） * 进行设置 `retries` 。
`enable.idempotence` | false | | 当前不支持幂等性。
`compression.type` | `none` | | 当前不支持压缩。

### <a name="consumer-configurations-only"></a>仅使用者配置
可在[此处](https://kafka.apache.org/documentation/#consumerconfigs)找到使用者配置。

properties | 建议值 | 允许的范围 | 注释
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 | | 3000为默认值，不应更改。
`session.timeout.ms` | 30000 |6000.。 300000| 从30000开始，如果由于丢失的检测信号而频繁地进行重新平衡，则增加。


## <a name="librdkafka-configuration-properties"></a>librdkafka 配置属性
主 `librdkafka` 配置文件（[链接](https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md)）包含以下属性的扩展说明。

### <a name="producer-and-consumer-configurations"></a>制造者和使用者配置

properties | 建议值 | 允许的范围 | 注释
---|---:|-----:|---
`socket.keepalive.enable` | true | | 如果应连接到空闲，则为必需。  Azure 会关闭入站 TCP 空闲 > 240000 毫秒。
`metadata.max.age.ms` | ~ 180000| < 240000 | 可以降低，以便更快地获取元数据更改。

### <a name="producer-configurations-only"></a>仅限生成方配置

properties | 建议值 | 允许的范围 | 注释
---|---:|-----:|---
`retries` | > 0 | | 默认值为 2。 建议保留此值。 
`request.timeout.ms` | 30000.。 60000 | > 20000| EH 在内部默认为最小为20000毫秒。  `librdkafka`默认值为5000，这可能会产生问题。 *尽管接受较低超时值的请求，但不保证客户端行为。*
`partitioner` | `consistent_random` | 请参阅 librdkafka 文档 | `consistent_random`是默认值，最佳。  在大多数情况下，将处理空键和 null 键。
`enable.idempotence` | false | | 当前不支持幂等性。
`compression.codec` | `none` || 当前不支持压缩。

### <a name="consumer-configurations-only"></a>仅使用者配置

properties | 建议值 | 允许的范围 | 注释
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 || 3000为默认值，不应更改。
`session.timeout.ms` | 30000 |6000.。 300000| 从30000开始，如果由于丢失的检测信号而频繁地进行重新平衡，则增加。


## <a name="further-notes"></a>详细说明

请查看下表中常见的与配置相关的错误情况。

症状 | 问题 | 解决方案
----|---|-----
由于重新平衡而导致提交失败 | 使用者在轮询（）的调用之间等待的时间太长，该服务开始例行组中的使用者。 | 有几种选项： <ul><li>增加会话超时</li><li>减小消息批大小以提高处理速度</li><li>提高处理并行性，避免阻塞使用者。投票（）</li></ul> 应用这三种的组合可能 wisest。
高产生吞吐量的网络异常 | 你是否正在使用 Java client + 默认的最大大小。  你的请求可能太大。 | 请参阅上述 Java 配置。

## <a name="next-steps"></a>后续步骤
有关所有 Azure 服务的配额和限制，请参阅 [Azure 订阅和服务限制、配额和约束](..//azure-resource-manager/management/azure-subscription-service-limits.md)。 

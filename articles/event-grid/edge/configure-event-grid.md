---
title: 配置 - Azure 事件网格 IoT 边缘 |微软文档
description: IoT 边缘事件网格中的配置。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76846467"
---
# <a name="event-grid-configuration"></a>事件网格配置

事件网格提供了许多配置，可以按环境进行修改。 以下部分是对所有可用选项及其默认值的引用。

## <a name="tls-configuration"></a>TLS 配置

要了解客户端身份验证，请参阅[安全和身份验证](security-authentication.md)。 其用法示例可[在本文中](configure-api-protocol.md)找到。

| 属性名称 | 描述 |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| 事件网格模块的 TLS 策略。 默认值仅为 HTTPS。
|`inbound__serverAuth__serverCert__source`| 事件网格模块用于其 TLS 配置的服务器证书源。 默认值为 IoT 边缘。

## <a name="incoming-client-authentication"></a>传入客户端身份验证

要了解客户端身份验证，请参阅[安全和身份验证](security-authentication.md)。 [在本文中](configure-client-auth.md)可以找到示例。

| 属性名称 | 描述 |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| 打开/关闭基于证书的客户端身份验证。 默认值为 true。
|`inbound__clientAuth__clientCert__source`| 用于验证客户端证书的源。 默认值为 IoT 边缘。
|`inbound__clientAuth__clientCert__allowUnknownCA`| 允许自签名客户端证书的策略。 默认值为 true。
|`inbound__clientAuth__sasKeys__enabled`| 打开/关闭基于 SAS 密钥的客户端身份验证。 默认值已关闭。
|`inbound__clientAuth__sasKeys__key1`| 用于验证传入请求的值之一。
|`inbound__clientAuth__sasKeys__key2`| 用于验证传入请求的可选第二个值。

## <a name="outgoing-client-authentication"></a>传出客户端身份验证
要了解客户端身份验证，请参阅[安全和身份验证](security-authentication.md)。 [在本文中](configure-identity-auth.md)可以找到示例。

| 属性名称 | 描述 |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| 打开/关闭为传出请求附加标识证书。 默认值为 true。
|`outbound__clientAuth__clientCert__source`| 用于检索事件网格模块的传出证书的源。 默认值为 IoT 边缘。

## <a name="webhook-event-handlers"></a>Webhook 事件处理程序

要了解客户端身份验证，请参阅[安全和身份验证](security-authentication.md)。 [在本文中](configure-webhook-subscriber-auth.md)可以找到示例。

| 属性名称 | 描述 |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| 用于控制是否仅允许 HTTPS 订阅者的策略。 默认值为 true（仅 HTTPS）。
|`outbound__webhook__skipServerCertValidation`| 标志以控制是否验证订阅者的证书。 默认值为 true。
|`outbound__webhook__allowUnknownCA`| 用于控制订阅者是否可以显示自签名证书的策略。 默认值为 true。 

## <a name="delivery-and-retry"></a>传递和重试

要了解此功能，请参阅[交付和重试](delivery-retry.md)。

| 属性名称 | 描述 |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 传递事件的最大尝试次数。 默认值为 30。
| `broker__defaultEventTimeToLiveInSeconds` | 生存时间 （TTL） 以秒为单位，之后如果未传递事件，则丢弃事件。 默认值为**7200**秒

## <a name="output-batching"></a>输出批处理

要了解此功能，请参阅[交付和输出批处理](delivery-output-batching.md)。

| 属性名称 | 描述 |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | `ApproxBatchSizeInBytes`旋钮允许的最大值。 默认值为 `1_058_576`。
| `api__deliveryPolicyLimits__maxEventsPerBatch` | `MaxEventsPerBatch`旋钮允许的最大值。 默认值为 `50`。
| `broker__defaultMaxBatchSizeInBytes` | 仅`MaxEventsPerBatch`指定时的最大传递请求大小。 默认值为 `1_058_576`。
| `broker__defaultMaxEventsPerBatch` | 仅`MaxBatchSizeInBytes`指定时要添加到批处理的最大事件数。 默认值为 `10`。

## <a name="metrics"></a>指标

要了解如何在 IoT 边缘上使用事件网格指标，请参阅[监视主题和订阅](monitor-topics-subscriptions.md)

| 属性名称 | 描述 |
| ---------------- | ------------ |
| `metrics__reporterType` | 报告者类型为指标 enpoint。 默认值为`none`并禁用指标。 设置为`prometheus`启用普罗米古斯博览会格式的指标。
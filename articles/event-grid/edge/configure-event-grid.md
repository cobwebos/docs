---
title: 配置-Azure 事件网格 IoT Edge |Microsoft Docs
description: IoT Edge 上的事件网格中的配置。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 632227579fd021a0d2ce1d0b1bb0b8a8288c5f47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171663"
---
# <a name="event-grid-configuration"></a>事件网格配置

事件网格提供了许多可根据环境进行修改的配置。 以下部分是对所有可用选项及其默认值的引用。

## <a name="tls-configuration"></a>TLS 配置

若要了解一般的客户端身份验证，请参阅 [安全性和身份验证](security-authentication.md)。 可在 [此文](configure-api-protocol.md)中找到其用法的示例。

| 属性名称 | 描述 |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| 事件网格模块的 TLS 策略。 默认值为 "仅 HTTPS"。
|`inbound__serverAuth__serverCert__source`| 事件网格模块用于其 TLS 配置的服务器证书源。 默认值为 IoT Edge。

## <a name="incoming-client-authentication"></a>传入客户端身份验证

若要了解一般的客户端身份验证，请参阅 [安全性和身份验证](security-authentication.md)。 可在 [此文](configure-client-auth.md)中找到示例。

| 属性名称 | 描述 |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| 启用/关闭基于证书的客户端身份验证。 默认值为 true。
|`inbound__clientAuth__clientCert__source`| 用于验证客户端证书的源。 默认值为 IoT Edge。
|`inbound__clientAuth__clientCert__allowUnknownCA`| 允许自签名客户端证书的策略。 默认值为 true。
|`inbound__clientAuth__sasKeys__enabled`| 启用/禁用基于 SAS 密钥的客户端身份验证。 默认值为 off。
|`inbound__clientAuth__sasKeys__key1`| 用于验证传入请求的值之一。
|`inbound__clientAuth__sasKeys__key2`| 用于验证传入请求的可选的第二个值。

## <a name="outgoing-client-authentication"></a>传出客户端身份验证
若要了解一般的客户端身份验证，请参阅 [安全性和身份验证](security-authentication.md)。 可在 [此文](configure-identity-auth.md)中找到示例。

| 属性名称 | 描述 |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| 为传出请求开启/关闭标识证书。 默认值为 true。
|`outbound__clientAuth__clientCert__source`| 用于检索事件网格模块的传出证书的源。 默认值为 IoT Edge。

## <a name="webhook-event-handlers"></a>Webhook 事件处理程序

若要了解一般的客户端身份验证，请参阅 [安全性和身份验证](security-authentication.md)。 可在 [此文](configure-webhook-subscriber-auth.md)中找到示例。

| 属性名称 | 描述 |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| 用于控制是否只允许 HTTPS 订阅服务器的策略。 默认值为 true (仅限 HTTPS) 。
|`outbound__webhook__skipServerCertValidation`| 用于控制是否验证订阅者证书的标志。 默认值为 true。
|`outbound__webhook__allowUnknownCA`| 用于控制订户是否可以显示自签名证书的策略。 默认值为 true。 

## <a name="delivery-and-retry"></a>传递和重试

若要深入了解此功能，请参阅 [传递和重试](delivery-retry.md)。

| 属性名称 | 描述 |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 尝试传递事件的最大次数。 默认值为 30。
| `broker__defaultEventTimeToLiveInSeconds` | 生存时间 (TTL) 秒数，在这段时间之后，如果未传递事件，事件将被丢弃。 默认值为  **7200** 秒

## <a name="output-batching"></a>输出批处理

若要深入了解此功能，请参阅 [传递和输出批处理](delivery-output-batching.md)。

| 属性名称 | 描述 |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | 旋钮允许的最大值 `ApproxBatchSizeInBytes` 。 默认值为 `1_058_576`。
| `api__deliveryPolicyLimits__maxEventsPerBatch` | 旋钮允许的最大值 `MaxEventsPerBatch` 。 默认值为 `50`。
| `broker__defaultMaxBatchSizeInBytes` | 仅指定时的最大传递请求大小 `MaxEventsPerBatch` 。 默认值为 `1_058_576`。
| `broker__defaultMaxEventsPerBatch` | 仅指定了时要添加到批的最大事件数 `MaxBatchSizeInBytes` 。 默认值为 `10`。

## <a name="metrics"></a>指标

若要了解有关 IoT Edge 上的事件网格使用度量值的信息，请参阅 [监视主题和订阅](monitor-topics-subscriptions.md)

| 属性名称 | 描述 |
| ---------------- | ------------ |
| `metrics__reporterType` | 指标终结点的报告器类型。 默认值为 `none` 并禁用指标。 设置以 `prometheus` 启用 Prometheus 处于阐释格式的指标。
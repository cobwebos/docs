---
title: 配置-Azure 事件网格 IoT Edge |Microsoft Docs
description: IoT Edge 上的事件网格中的配置。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846467"
---
# <a name="event-grid-configuration"></a>事件网格配置

事件网格提供了许多可根据环境进行修改的配置。 以下部分是对所有可用选项及其默认值的引用。

## <a name="tls-configuration"></a>TLS 配置

若要了解一般的客户端身份验证，请参阅[安全性和身份验证](security-authentication.md)。 可在[此文](configure-api-protocol.md)中找到其用法的示例。

| 属性名称 | Description |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| 事件网格模块的 TLS 策略。 默认值为 "仅 HTTPS"。
|`inbound__serverAuth__serverCert__source`| 事件网格模块用于其 TLS 配置的服务器证书源。 默认值为 IoT Edge。

## <a name="incoming-client-authentication"></a>传入客户端身份验证

若要了解一般的客户端身份验证，请参阅[安全性和身份验证](security-authentication.md)。 可在[此文](configure-client-auth.md)中找到示例。

| 属性名称 | Description |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| 启用/关闭基于证书的客户端身份验证。 默认值为 true。
|`inbound__clientAuth__clientCert__source`| 用于验证客户端证书的源。 默认值为 IoT Edge。
|`inbound__clientAuth__clientCert__allowUnknownCA`| 允许自签名客户端证书的策略。 默认值为 true。
|`inbound__clientAuth__sasKeys__enabled`| 启用/禁用基于 SAS 密钥的客户端身份验证。 默认值为 off。
|`inbound__clientAuth__sasKeys__key1`| 用于验证传入请求的值之一。
|`inbound__clientAuth__sasKeys__key2`| 用于验证传入请求的可选的第二个值。

## <a name="outgoing-client-authentication"></a>传出客户端身份验证
若要了解一般的客户端身份验证，请参阅[安全性和身份验证](security-authentication.md)。 可在[此文](configure-identity-auth.md)中找到示例。

| 属性名称 | Description |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| 为传出请求开启/关闭标识证书。 默认值为 true。
|`outbound__clientAuth__clientCert__source`| 用于检索事件网格模块的传出证书的源。 默认值为 IoT Edge。

## <a name="webhook-event-handlers"></a>Webhook 事件处理程序

若要了解一般的客户端身份验证，请参阅[安全性和身份验证](security-authentication.md)。 可在[此文](configure-webhook-subscriber-auth.md)中找到示例。

| 属性名称 | Description |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| 用于控制是否只允许 HTTPS 订阅服务器的策略。 默认值为 true （仅 HTTPS）。
|`outbound__webhook__skipServerCertValidation`| 用于控制是否验证订阅者证书的标志。 默认值为 true。
|`outbound__webhook__allowUnknownCA`| 用于控制订户是否可以显示自签名证书的策略。 默认值为 true。 

## <a name="delivery-and-retry"></a>传递和重试

若要深入了解此功能，请参阅[传递和重试](delivery-retry.md)。

| 属性名称 | Description |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 尝试传递事件的最大次数。 默认值为 30。
| `broker__defaultEventTimeToLiveInSeconds` | 生存时间（TTL），在此时间之后，如果未传递事件，事件将被丢弃。 默认值为**7200**秒

## <a name="output-batching"></a>输出批处理

若要深入了解此功能，请参阅[传递和输出批处理](delivery-output-batching.md)。

| 属性名称 | Description |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | `ApproxBatchSizeInBytes` 旋钮允许的最大值。 默认值为 `1_058_576`。
| `api__deliveryPolicyLimits__maxEventsPerBatch` | `MaxEventsPerBatch` 旋钮允许的最大值。 默认值为 `50`。
| `broker__defaultMaxBatchSizeInBytes` | 仅指定 `MaxEventsPerBatch` 时的最大传递请求大小。 默认值为 `1_058_576`。
| `broker__defaultMaxEventsPerBatch` | 仅指定 `MaxBatchSizeInBytes` 时要添加到批中的事件的最大数量。 默认值为 `10`。

## <a name="metrics"></a>指标

若要了解有关 IoT Edge 上的事件网格使用度量值的信息，请参阅[监视主题和订阅](monitor-topics-subscriptions.md)

| 属性名称 | Description |
| ---------------- | ------------ |
| `metrics__reporterType` | 指标终结点的报告器类型。 默认值为 `none` 并禁用指标。 设置为 `prometheus` 会启用 Prometheus 处于阐释格式的指标。
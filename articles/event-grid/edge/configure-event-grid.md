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
ms.openlocfilehash: a9bac9a84e1ba034b011691ae82a1cb67eb71af0
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992426"
---
# <a name="event-grid-configuration"></a>事件网格配置

事件网格提供了许多可根据环境进行修改的配置。 以下部分是对所有可用选项及其默认值的引用。

## <a name="tls-configuration"></a>TLS 配置

若要了解一般的客户端身份验证，请参阅[安全性和身份验证](security-authentication.md)。 可在[此文](configure-api-protocol.md)中找到其用法的示例。

| 属性名称 | 描述 |
| ---------------- | ------------ |
|`inbound:serverAuth:tlsPolicy`| 事件网格模块的 TLS 策略。 默认值为 "仅 HTTPS"。
|`inbound:serverAuth:serverCert:source`| 事件网格模块用于其 TLS 配置的服务器证书源。 默认值为 IoT Edge。

## <a name="incoming-client-authentication"></a>传入客户端身份验证

若要了解一般的客户端身份验证，请参阅[安全性和身份验证](security-authentication.md)。 可在[此文](configure-client-auth.md)中找到示例。

| 属性名称 | 描述 |
| ---------------- | ------------ |
|`inbound:clientAuth:clientCert:enabled`| 启用/关闭基于证书的客户端身份验证。 默认值为 true。
|`inbound:clientAuth:clientCert:source`| 用于验证客户端证书的源。 默认值为 IoT Edge。
|`inbound:clientAuth:clientCert:allowUnknownCA`| 允许自签名客户端证书的策略。 默认值为 true。
|`inbound:clientAuth:sasKeys:enabled`| 启用/禁用基于 SAS 密钥的客户端身份验证。 默认值为 off。
|`inbound:clientAuth:sasKeys:key1`| 用于验证传入请求的值之一。
|`inbound:clientAuth:sasKeys:key2`| 用于验证传入请求的可选的第二个值。

## <a name="outgoing-client-authentication"></a>传出客户端身份验证
若要了解一般的客户端身份验证，请参阅[安全性和身份验证](security-authentication.md)。 可在[此文](configure-identity-auth.md)中找到示例。

| 属性名称 | 描述 |
| ---------------- | ------------ |
|`outbound:clientAuth:clientCert:enabled`| 为传出请求开启/关闭标识证书。 默认值为 true。
|`outbound:clientAuth:clientCert:source`| 用于检索事件网格模块的传出证书的源。 默认值为 IoT Edge。

## <a name="webhook-event-handlers"></a>Webhook 事件处理程序

若要了解一般的客户端身份验证，请参阅[安全性和身份验证](security-authentication.md)。 可在[此文](configure-webhook-subscriber-auth.md)中找到示例。

| 属性名称 | 描述 |
| ---------------- | ------------ |
|`outbound:webhook:httpsOnly`| 用于控制是否只允许 HTTPS 订阅服务器的策略。 默认值为 true （仅 HTTPS）。
|`outbound:webhook:skipServerCertValidation`| 用于控制是否验证订阅者证书的标志。 默认值为 true。
|`outbound:webhook:allowUnknownCA`| 用于控制订户是否可以显示自签名证书的策略。 默认值为 true。 

## <a name="delivery-and-retry"></a>传递和重试

若要深入了解此功能，请参阅[传递和重试](delivery-retry.md)。

| 属性名称 | 描述 |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | 尝试传递事件的最大次数。 默认值为 30。
| `broker:defaultEventTimeToLiveInSeconds` | 生存时间（TTL），在此时间之后，如果未传递事件，事件将被丢弃。 默认值为**7200**秒

## <a name="output-batching"></a>输出批处理

若要深入了解此功能，请参阅[传递和输出批处理](delivery-output-batching.md)。

| 属性名称 | 描述 |
| ---------------- | ------------ |
| `api:deliveryPolicyLimits:maxBatchSizeInBytes` | `ApproxBatchSizeInBytes` 旋钮允许的最大值。 默认值为 `1_058_576`。
| `api:deliveryPolicyLimits:maxEventsPerBatch` | `MaxEventsPerBatch` 旋钮允许的最大值。 默认值为 `50`。
| `broker:defaultMaxBatchSizeInBytes` | 仅指定 `MaxEventsPerBatch` 时的最大传递请求大小。 默认值为 `1_058_576`。
| `broker:defaultMaxEventsPerBatch` | 仅指定 `MaxBatchSizeInBytes` 时要添加到批中的事件的最大数量。 默认值为 `10`。

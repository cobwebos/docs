---
title: 安全性和身份验证 - Azure 事件网格 IoT 边缘 |微软文档
description: IoT 边缘事件网格中的安全性和身份验证。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5dfa17fd702b76e2cfaa7a91066dbc6749c1069e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844507"
---
# <a name="security-and-authentication"></a>安全性和身份验证

安全性和身份验证是一个高级概念，它首先需要熟悉事件网格基础知识。 如果您对 IoT 边缘上的事件网格是新数据库，则[从这里](concepts.md)开始。 事件网格模块基于 IoT Edge 上的现有安全基础结构构建。 有关详细信息和设置，请参阅[本文档](../../iot-edge/security.md)。

以下各节详细介绍了如何对这些设置进行保护和管理：

* TLS 配置
* 入站客户端身份验证
* 出站服务器身份验证
* 出站客户端身份验证

>[!IMPORTANT]
>事件网格模块安全性和身份验证利用了 IoT Edge 上可用的现有基础结构。 假设 IoT Edge 子系统是安全的。

>[!IMPORTANT]
>默认情况下，事件网格配置**是安全的**。 以下小节将解释可用于重写身份验证方面的所有选项和可能值。 在进行任何更改之前，了解影响。 要使任何更改生效，需要重新部署事件网格模块。

## <a name="tls-configuration-aka-server-authentication"></a>TLS 配置（即服务器身份验证）

事件网格模块同时承载 HTTP 和 HTTPS 终结点。 每个 IoT Edge 模块都由 IoT Edge 的安全守护进程分配服务器证书。 我们使用服务器证书来保护终结点。 到期后，模块将自动刷新 IoT Edge 安全守护进程的新证书。

默认情况下，只允许 HTTPS 通信。 您可以通过**inbound__serverAuth__tlsPolicy**配置覆盖此行为。 下表捕获此属性的可能值。

| 可能值 | 描述 |
| ---------------- | ------------ |
| 严格 | 默认。 仅启用 HTTPS
| 已启用 | 同时启用 HTTP 和 HTTPS
| 已禁用 | 仅启用 HTTP

## <a name="inbound-client-authentication"></a>入站客户端身份验证

客户端是执行管理和/或运行时操作的实体。 客户端可以是其他 IoT 边缘模块，非 IoT 应用程序。

事件网格模块支持两种类型的客户端身份验证：

* 基于密钥的共享访问签名 （SAS）
* 基于证书

默认情况下，事件网格模块配置为仅接受基于证书的身份验证。 启动时，事件网格模块从 IoT 边缘安全守护程序检索"信任捆绑包"，并使用它验证任何客户端证书。 不解析为此链的客户端证书将被拒绝。 `UnAuthorized`

### <a name="certificate-based-client-authentication"></a>基于证书的客户端身份验证

默认情况下，基于证书的身份验证处于打开状态。 您可以选择通过属性**inbound__clientAuth__clientCert__enabled**禁用基于证书的身份验证。 下表捕获可能的值。

| 可能值 | 描述 |
| ----------------  | ------------ |
| true | 默认。 要求事件网格模块中的所有请求都提供客户端证书。 此外，您需要配置**inbound__clientAuth__clientCert__source**。
| false | 不要强制客户端提供证书。

下表捕获**inbound__clientAuth__clientCert__source**的可能值

| 可能值 | 描述 |
| ---------------- | ------------ |
| IoT Edge | 默认。 使用 IoT 边缘的信任捆绑包验证所有客户端证书。

默认情况下，如果客户端显示自签名，事件网格模块将拒绝此类请求。 您可以选择通过**inbound__clientAuth__clientCert__allowUnknownCA**属性允许自签名客户端证书。 下表捕获可能的值。

| 可能值 | 描述 |
| ----------------  | ------------|
| true | 默认。 允许成功显示自签名证书。
| false | 如果显示自签名证书，则请求失败。

>[!IMPORTANT]
>在生产方案中，您可能希望**将inbound__clientAuth__clientCert__allowUnknownCA**设置为**false**。

### <a name="sas-key-based-client-authentication"></a>基于 SAS 密钥的客户端身份验证

除了基于证书的身份验证外，事件网格模块还可以执行基于 SAS 密钥的身份验证。 SAS 密钥类似于事件网格模块中配置的机密，它应该用于验证所有传入呼叫。 客户端需要在 HTTP 标头"aeg-sas-key"中指定机密。 如果请求不匹配，`UnAuthorized`将被拒绝。

控制基于 SAS 密钥的身份验证的配置**inbound__clientAuth__sasKeys__enabled**。

| 可能值 | 描述  |
| ----------------  | ------------ |
| true | 允许基于 SAS 密钥的身份验证。 需要**inbound__clientAuth__sasKeys__key1**或**inbound__clientAuth__sasKeys__key2**
| false | 默认。 禁用基于 SAS 密钥的身份验证。

 **inbound__clientAuth__sasKeys__key1**和**inbound__clientAuth__sasKeys__key2**是配置事件网格模块以检查传入请求的密钥。 至少需要配置其中一个密钥。 发出请求的客户端需要将密钥作为请求标头 **"aeg-sas-key"** 的一部分呈现。 如果同时配置了两个密钥，则客户端可以显示任一个密钥。

> [!NOTE]
>可以配置两种身份验证方法。 在这种情况下，首先检查 SAS 密钥，并且仅当失败时，才执行基于证书的身份验证。 请求要成功，只需一种身份验证方法才能成功。

## <a name="outbound-client-authentication"></a>出站客户端身份验证

出站上下文中的客户端引用事件网格模块。 正在执行的操作是向订阅者传递事件。 订阅模块被视为服务器。

每个 IoT 边缘模块都由 IoT Edge 的安全守护进程分配一个标识证书。 我们将标识证书用于传出呼叫。 到期后，模块将自动刷新 IoT Edge 安全守护进程的新证书。

控制出站客户端身份验证的配置**outbound__clientAuth__clientCert__enabled**。

| 可能值 | 描述 |
| ----------------  | ------------ |
| true | 默认。 需要来自事件网格模块的所有传出请求来显示证书。 需要配置**outbound__clientAuth__clientCert__source**。
| false | 不需要事件网格模块来显示其证书。

控制证书源的配置**outbound__clientAuth__clientCert__source。**

| 可能值 | 描述 |
| ---------------- | ------------ |
| IoT Edge | 默认。 使用由 IoT Edge 安全守护进程配置的模块标识证书。

### <a name="outbound-server-authentication"></a>出站服务器身份验证

事件网格订阅者的目标类型之一是"Webhook"。 默认情况下，此类订阅者仅接受 HTTPS 终结点。

用于控制 webhook 目标策略**的配置outbound__webhook__httpsOnly**。

| 可能值 | 描述 |
| ----------------  | ------------ |
| true | 默认。 仅允许具有 HTTPS 终结点的订阅者。
| false | 允许具有 HTTP 或 HTTPS 终结点的订阅者。

默认情况下，事件网格模块将验证订阅者的服务器证书。 您可以通过重写**outbound__webhook__skipServerCertValidation**跳过验证。 可能的值包括：

| 可能值 | 描述 |
| ----------------  | ------------ |
| true | 不要验证订阅者的服务器证书。
| false | 默认。 验证订阅者的服务器证书。

如果订阅者的证书是自签名的，则默认情况下事件网格模块将拒绝此类订阅者。 要允许自签名证书，可以重写**outbound__webhook__allowUnknownCA**。 下表捕获了可能的值。

| 可能值 | 描述 |
| ----------------  | ------------ |
| true | 默认。 允许成功显示自签名证书。
| false | 如果显示自签名证书，则请求失败。

>[!IMPORTANT]
>在生产方案中，您需要将**outbound__webhook__allowUnknownCA**设置为**false**。

> [!NOTE]
>IoT 边缘环境生成自签名证书。 建议是生成由授权 CA 为生产工作负载颁发的证书，并将入站和出站上的**未知CA**属性设置为**false。**

## <a name="summary"></a>总结

默认情况下，事件网格模块**是安全的**。 我们建议保留这些默认值，用于生产部署。

以下是配置时使用的指导原则：

* 只允许 HTTPS 请求进入模块。
* 仅允许基于证书的客户端身份验证。 仅允许由知名的 C 颁发的证书。 禁止自签名证书。
* 禁止基于 SASKey 的客户端身份验证。
* 始终在传出呼叫上显示事件网格模块的标识证书。
* 仅允许 HTTPS 订阅者访问 Webhook 目标类型。
* 始终验证 Webhook 目标类型的订阅者的服务器证书。 仅允许由知名的 C 颁发的证书。 禁止自签名证书。

默认情况下，事件网格模块部署具有以下配置：

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

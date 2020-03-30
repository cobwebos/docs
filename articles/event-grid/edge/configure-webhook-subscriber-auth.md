---
title: 配置 Webhook 订阅者身份验证 - Azure 事件网格 IoT 边缘 |微软文档
description: 配置 webhook 订阅服务器身份验证
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 101dcae5870322878cec48098f2efae32cc68c14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841724"
---
# <a name="configure-webhook-subscriber-authentication"></a>配置 webhook 订阅服务器身份验证

本指南提供了事件网格模块的可能 Webhook 订阅者配置的示例。 默认情况下，Webhook 订阅者仅接受 HTTPS 终结点。 如果订阅者提供自签名证书，事件网格模块将拒绝。

## <a name="allow-only-https-subscriber"></a>仅允许 HTTPS 订户

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>允许具有自签名证书的 HTTPS 订阅者

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>将属性`outbound__webhook__allowUnknownCA`设置为`true`仅在测试环境中，因为通常可以使用自签名证书。 对于生产工作负载，我们建议将它们设置为**false**。

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>允许 HTTPS 订阅者，但跳过证书验证

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>将属性`outbound__webhook__skipServerCertValidation`设置为`true`仅在测试环境中，因为您可能没有提供需要身份验证的证书。 对于生产工作负载，我们建议将它们设置为**false**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>允许使用自签名证书的 HTTP 和 HTTPS

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>将属性`outbound__webhook__httpsOnly`设置为`false`仅在测试环境中，因为您可能想要首先启动 HTTP 订阅服务器。 对于生产工作负载，我们建议将它们设置为**true**

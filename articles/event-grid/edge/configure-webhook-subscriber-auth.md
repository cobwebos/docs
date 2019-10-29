---
title: 配置 webhook 订户身份验证-Azure 事件网格 IoT Edge |Microsoft Docs
description: 配置 webhook 订阅服务器身份验证
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 97ed1e2ad84d895e9da0d96cd070e14acb46385d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992478"
---
# <a name="configure-webhook-subscriber-authentication"></a>配置 webhook 订阅服务器身份验证

本指南提供事件网格模块的可能的 webhook 订户配置示例。 默认情况下，webhook 订阅服务器仅接受 HTTPS 终结点。 如果订阅服务器提供了自签名证书，则事件网格模块将被拒绝。

## <a name="allow-only-https-subscriber"></a>仅允许 HTTPS 订阅服务器

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>允许带有自签名证书的 HTTPS 订阅服务器

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>将属性 `outbound:webhook:allowUnknownCA` 设置为仅在测试环境中 `true`，因为通常可以使用自签名证书。 对于生产工作负荷，我们建议将其设置为**false**。

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>允许 HTTPS 订阅服务器，但跳过证书验证

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=true",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>将属性 `outbound:webhook:skipServerCertValidation` 设置为仅在测试环境中 `true`，因为你可能无法呈现需要进行身份验证的证书。 对于生产工作负荷，我们建议将其设置为**false**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>允许 HTTP 和 HTTPS 与自签名证书

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=false",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>将属性 `outbound:webhook:httpsOnly` 设置为仅在测试环境中 `false`，因为你可能希望首先打开 HTTP 订阅服务器。 对于生产工作负荷，我们建议将其设置为**true**

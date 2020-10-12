---
title: 配置 webhook 订户身份验证-Azure 事件网格 IoT Edge |Microsoft Docs
description: 配置 webhook 订阅服务器身份验证
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: d78b6e80005e9533ccd5ebfaea853f35ae6004be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171646"
---
# <a name="configure-webhook-subscriber-authentication"></a>配置 webhook 订阅服务器身份验证

本指南提供事件网格模块的可能的 webhook 订户配置示例。 默认情况下，webhook 订阅服务器仅接受 HTTPS 终结点。 如果订阅服务器提供了自签名证书，则事件网格模块将被拒绝。

## <a name="allow-only-https-subscriber"></a>仅允许 HTTPS 订阅服务器

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>允许带有自签名证书的 HTTPS 订阅服务器

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
>`outbound__webhook__allowUnknownCA` `true` 只能在测试环境中将属性设置为，因为通常可以使用自签名证书。 对于生产工作负荷，我们建议将其设置为 **false**。

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>允许 HTTPS 订阅服务器，但跳过证书验证

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
>`outbound__webhook__skipServerCertValidation` `true` 仅在测试环境中将属性设置为，因为你可能无法提供需要进行身份验证的证书。 对于生产工作负荷，我们建议将其设置为 **false**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>允许 HTTP 和 HTTPS 与自签名证书

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
>`outbound__webhook__httpsOnly` `false` 仅在测试环境中将属性设置为，因为可能需要首先打开 HTTP 订阅服务器。 对于生产工作负荷，我们建议将其设置为 **true**

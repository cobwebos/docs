---
title: 配置传入呼叫的客户端身份验证-Azure 事件网格 IoT Edge |Microsoft Docs
description: 配置 IoT Edge 上的事件网格公开的 API 协议。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: b5456130e89bf77e2c2ba41880323e38f6b27f4c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992504"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>配置传入呼叫的客户端身份验证

本指南提供事件网格模块的可能的客户端身份验证配置示例。 事件网格模块支持两种类型的客户端身份验证：

* 基于共享访问签名（SAS）密钥
* 基于证书

有关所有可能的配置，请参阅[安全性和身份验证](security-authentication.md)指南。

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>启用基于证书的客户端身份验证，无自签名证书

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>启用基于证书的客户端身份验证，允许自签名证书

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>仅在测试环境中将属性**inbound： clientAuth： x-arr-clientcert： allowUnknownCA**设置为**true** ，因为通常可以使用自签名证书。 对于生产工作负荷，我们建议将此属性设置为**false** ，并将证书颁发机构（CA）。

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>启用基于证书和基于 sas 密钥的客户端身份验证

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=true",
    "inbound:clientAuth:sasKeys:key1=<some-secret1-here>",
    "inbound:clientAuth:sasKeys:key2=<some-secret2-here>",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>基于 SAS 密钥的客户端身份验证允许非 IoT edge 模块执行管理和运行时操作，假设可以在 IoT Edge 网络外部访问 API 端口。

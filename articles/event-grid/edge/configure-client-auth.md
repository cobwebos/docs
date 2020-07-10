---
title: 配置传入呼叫的客户端身份验证-Azure 事件网格 IoT Edge |Microsoft Docs
description: 配置 IoT Edge 上的事件网格公开的 API 协议。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: a0bba9559c2a0b4ff6c8a4e5f2287692e27f8a1a
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171697"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>配置传入呼叫的客户端身份验证

本指南提供事件网格模块的可能的客户端身份验证配置示例。 事件网格模块支持两种类型的客户端身份验证：

*  (SAS) 基于密钥的共享访问签名
* 基于证书

有关所有可能的配置，请参阅[安全性和身份验证](security-authentication.md)指南。

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>启用基于证书的客户端身份验证，无自签名证书

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>启用基于证书的客户端身份验证，允许自签名证书

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>仅在测试环境中将属性**inbound__clientAuth__clientCert__allowUnknownCA**设置为**true** ，因为通常可以使用自签名证书。 对于生产工作负荷，我们建议将此属性设置为**false** ，并将证书颁发机构的证书 (CA) 。

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>启用基于证书和基于 sas 密钥的客户端身份验证

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>基于 SAS 密钥的客户端身份验证允许非 IoT edge 模块执行管理和运行时操作，假设可以在 IoT Edge 网络外部访问 API 端口。

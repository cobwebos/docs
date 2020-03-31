---
title: 配置传入呼叫的客户端身份验证 - Azure 事件网格 IoT 边缘 |微软文档
description: 配置 IoT 边缘上事件网格公开的 API 协议。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841785"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>配置传入呼叫的客户端身份验证

本指南提供了事件网格模块的可能客户端身份验证配置的示例。 事件网格模块支持两种类型的客户端身份验证：

* 基于密钥的共享访问签名 （SAS）
* 基于证书

有关所有可能的配置，请参阅[安全和身份验证](security-authentication.md)指南。

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>启用基于证书的客户端身份验证，无需自签名证书

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
>将**inbound__clientAuth__clientCert__allowUnknownCA属性**设置为仅在测试环境中**为 true，** 因为通常可以使用自签名证书。 对于生产工作负载，我们建议您将此属性设置为**false**和证书颁发机构 （CA） 的证书。

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>启用基于证书和基于沙键的客户端身份验证

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
>基于 SAS 密钥的客户端身份验证允许非 IoT 边缘模块执行管理和运行时操作，前提是 API 端口当然可以在 IoT 边缘网络之外访问。

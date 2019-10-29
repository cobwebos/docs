---
title: 配置标识-Azure 事件网格 IoT Edge |Microsoft Docs
description: 配置事件网格模块的标识
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 336b6157128468169264d6ffa9564da4d9338aae
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992439"
---
# <a name="configure-identity-for-the-event-grid-module"></a>配置事件网格模块的标识

本文提供事件网格模块的可能标识配置示例。 默认情况下，事件网格模块将显示 IoT security daemon 配置的标识证书。 标识证书由事件网格模块在其传出的调用（即传递事件时）提供。 然后，事件网格事件的订阅者可以选择验证它是否确实是在接受事件前发送事件的事件网格模块。

有关所有可能的配置，请参阅[安全性和身份验证](security-authentication.md)指南。

## <a name="always-present-identity-certificate"></a>始终显示标识证书
下面是一个示例配置，适用于在传出呼叫上始终呈现标识证书。 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>不显示标识证书
下面是一个示例配置，用于在传出调用中显示标识证书。 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=false"
  ]
}
 ```

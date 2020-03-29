---
title: 配置标识 - Azure 事件网格 IoT 边缘 |微软文档
description: 配置事件网格模块的标识
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841759"
---
# <a name="configure-identity-for-the-event-grid-module"></a>配置事件网格模块的标识

本文演示如何为 Edge 上的网格配置标识。 默认情况下，事件网格模块显示其标识证书，由 IoT 安全守护进程配置。 边缘上的事件网格在传递事件时显示其标识证书及其传出调用。 然后，订阅者可以验证它是在接收之前发送事件的事件网格模块。

有关所有可能的配置，请参阅[安全和身份验证](security-authentication.md)指南。

## <a name="always-present-identity-certificate"></a>始终显示身份证书
下面是始终在传出调用上显示标识证书的示例配置。 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>不显示身份证书
下面是未在传出调用上显示标识证书的示例配置。 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```

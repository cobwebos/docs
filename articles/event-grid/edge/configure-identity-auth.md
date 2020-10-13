---
title: 配置标识-Azure 事件网格 IoT Edge |Microsoft Docs
description: 配置事件网格模块的标识
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2418c8518bbf298a102d4f29b4a973f6121de2eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171680"
---
# <a name="configure-identity-for-the-event-grid-module"></a>配置事件网格模块的标识

本文介绍如何在边缘上为网格配置标识。 默认情况下，事件网格模块会提供 IoT security daemon 配置的标识证书。 边缘的事件网格在传递事件时，会向其提供其传出调用的标识证书。 然后，订阅者可以验证它是否是在接受前发送事件的事件网格模块。

有关所有可能的配置，请参阅 [安全性和身份验证](security-authentication.md) 指南。

## <a name="always-present-identity-certificate"></a>始终显示标识证书
下面是一个示例配置，适用于在传出呼叫上始终呈现标识证书。 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>不显示标识证书
下面是一个示例配置，用于在传出调用中显示标识证书。 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```

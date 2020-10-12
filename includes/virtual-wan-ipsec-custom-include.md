---
title: include 文件
description: include 文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e2a950037aed2a8ded4d4e55920721285cbfc05c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "82204544"
---
使用自定义 IPsec 策略时，请记住以下要求：

* **IKE** - 对于 IKE，你可以从“IKE 加密”中选择任何参数、从“IKE 完整性”中选择任何参数，以及从“DH 组”中选择任何参数。
* **Ipsec** -对于 ipsec，可以从 ipsec 加密中选择任何参数，并从 ipsec 完整性和 PFS 中选择任何参数。 如果“IPsec 加密”或“IPsec 完整性”的任一参数是 GCM，则这两个设置的参数必须都是 GCM。

>[!NOTE]
> 对于自定义 IPsec 策略，不存在响应方和发起方的概念（与默认 IPsec 策略不同）。 两端（本地和 Azure VPN 网关）将对“IKE 阶段 1”和“IKE 阶段 2”使用相同的设置。 IKEv1 协议和 IKEv2 协议均受支持。 不支持仅将 Azure 作为响应方。
>

**可用的设置和参数**

| 设置 | parameters |
|--- |--- |
| IKE 加密 | GCMAES256、GCMAES128、AES256、AES128 |
| IKE 完整性 | SHA384、SHA256 |
| DH 组 | ECP384、ECP256、DHGroup24、DHGroup14 |
| IPsec 加密 | GCMAES256、GCMAES128、AES256、AES128、None |
| IPsec 完整性 | GCMAES256、GCMAES128、SHA256 |
| PFS 组 | ECP384、ECP256、PFS24、PFS14、None |

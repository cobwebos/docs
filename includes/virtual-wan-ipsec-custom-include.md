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
ms.openlocfilehash: f7167cbb26e69941cade01ab8c0b8d9dc633f0d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72168405"
---
使用自定义 IPsec 策略时，请记住以下要求：

* **IKE** - 对于 IKE，您可以从 IKE 加密中选择任何参数，以及 IKE 完整性中的任何参数，以及 DH 组中的任何参数。
* **IPsec** - 对于 IPsec，您可以从 IPsec 加密中选择任何参数，以及 IPsec 完整性以及 PFS 中的任何参数。 如果 IPsec 加密或 IPsec 完整性的任何参数是 GCM，则这两个设置的参数必须是 GCM。

>[!NOTE]
> 使用自定义 IPsec 策略时，没有响应者和开始器的概念（与默认 IPsec 策略不同）。 两侧（本地和 Azure VPN 网关）将为 IKE 阶段 1 和 IKE 阶段 2 使用相同的设置。 支持 IKEv1 和 IKEv2 协议。 不支持 Azure 仅作为响应程序。
>

**可用设置和参数**

| 设置 | 参数 |
|--- |--- |
| IKE 加密 | AES256， AES192， AES128 |
| IKE 诚信 | SHA384， SHA256， SHA1 |
| DH 组 | DHGroup24、 ECP384、 ECP256、 DHGroup14、 DHGroup2048、 DHGroup2 |
| IPsec 加密 | GCMAES256， GCMAES192， GCMAES128， AES256， AES192， AES128 |
| IPsec 完整性 | GCMASE256， GCMAES192， GCMAES128， SHA256， SHA1 |
| PFS 组 | PFS24、ECP384、ECP256、PFS2048、PFS2 |

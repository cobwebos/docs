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
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168405"
---
使用自定义 IPsec 策略时，请记住以下要求：

* **Ike** -对于 ike，你可以从 ike 加密中选择任何参数，并从 ike 完整性中选择任何参数，以及从 DH 组中选择任何参数。
* **Ipsec** -对于 ipsec，可以从 ipsec 加密中选择任何参数，并从 ipsec 完整性和 PFS 中选择任何参数。 如果 IPsec 加密或 IPsec 完整性的任何参数都是 GCM，则这两个设置的参数必须是 GCM。

>[!NOTE]
> 对于自定义 IPsec 策略，不存在响应方和发起方的概念（与默认 IPsec 策略不同）。 两侧（本地和 Azure VPN 网关）将为 IKE 阶段1和 IKE 阶段2使用相同的设置。 支持 IKEv1 和 IKEv2 协议。 不支持仅将 Azure 作为响应方。
>

**可用的设置和参数**

| 设置 | Parameters |
|--- |--- |
| IKE 加密 | AES256、AES192、AES128 |
| IKE 完整性 | SHA384、SHA256、SHA1 |
| DH 组 | DHGroup24、ECP384、ECP256、DHGroup14、DHGroup2048、DHGroup2 |
| IPsec 加密 | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128 |
| IPsec 完整性 | GCMASE256、GCMAES192、GCMAES128、SHA256、SHA1 |
| PFS 组 | PFS24、ECP384、ECP256、PFS2048、PFS2 |

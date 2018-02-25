---
title: "来宾操作系统支持 Azure 堆栈 |Microsoft 文档"
description: "在 Azure 堆栈上，可以使用这些来宾操作系统的系统。"
services: azure-stack
documentationcenter: 
author: Brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: 3eceb740b8115d2eaca517017f6158744d6e8e58
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>在 Azure 堆栈上受支持的来宾操作系统

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

## <a name="windows"></a>Windows
Azure 堆栈支持下表中列出了 Windows 来宾操作系统： 应用商店中的映像是可供下载到 Azure 堆栈。 Windows 客户端映像应用商店中不可用。

在部署期间，Azure 堆栈将了适当版本的来宾代理注入到映像。

| 操作系统 | 说明 | 发布者 | OS 类型 | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server 2008 R2 SP1 | 64 位 | Microsoft | Windows | 数据中心 |
| Windows Server 2012 | 64 位 | Microsoft | Windows | 数据中心 |
| Windows Server 2012 R2 | 64 位 | Microsoft | Windows | 数据中心 |
| Windows Server 2016 | 64 位 | Microsoft | Windows | 数据中心，数据中心核心数据中心与容器 |
| Windows 10 *（参见备注 1）* | 64 位，Pro 和 Enterprise | Microsoft | Windows | 否 |

***注释 1:****要部署 Azure 堆栈上的 Windows 10 客户端操作系统，必须拥有[每用户许可 Windows](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx)或购买一个限定的多租户托管商通过 ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).* 


## <a name="linux"></a>Linux

此处列出的 Linux 分发版包括必要 Windows Azure Linux 代理 (WALA)。

> [!NOTE]   
> 构建与 WALA 版本较旧的多于 2.2.3 映像*不*支持和不太可能部署。 不在 Azure 堆栈的虚拟机，包括版本 2.2.12 和 2.2.13 函数已知某些 WALA 代理版本。
>
> [cloud-init](https://cloud-init.io/)仅支持 Azure 堆栈上的 Ubuntu 分发。

| 分发 | 说明 | 发布者 | Marketplace |
| --- | --- | --- | --- | --- | --- |
| 容器 Linux |  64 位 | CoreOS | Stable |
| 基于 centOS 的 6.9 | 64 位 | 恶意批 | 是 |
| 基于 centOS 的 7.4 | 64 位 | 恶意批 | 是 |
| Debian 8 "Jessie" | 64 位 | credativ |  是 |
| Debian 9"Stretch" | 64 位 | credativ | 是 |
| Red Hat Enterprise Linux （待定） 7.x | 64 位 | Red Hat | 否 |
| SLES 11SP4 | 64 位 | SUSE | 是 |
| SLES 12SP3 | 64 位 | SUSE | 是 |
| Ubuntu 14.04 LTS | 64 位 | Canonical | 是 |
| Ubuntu 16.04 LTS | 64 位 | Canonical | 是 |

可能在将来支持的其他 Linux 发行版。

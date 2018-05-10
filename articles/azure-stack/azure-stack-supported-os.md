---
title: Azure Stack 支持的来宾操作系统 | Microsoft Docs
description: 在 Azure Stack 上可以使用这些来宾操作系统。
services: azure-stack
documentationcenter: ''
author: Brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: 819d8a9580de435536bf184b10ffbc23c32b247b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Azure Stack 支持的来宾操作系统

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="windows"></a>Windows

Azure 堆栈支持下表中列出的 Windows 来宾操作系统：

| 操作系统 | 说明 | 在应用商店中可用 |
| --- | --- | --- | --- | --- | --- |
| Windows Server，版本 1709 | 64 位 | 与容器的核心 |
| Windows Server 2016 | 64 位 |  数据中心、数据中心核心、包含容器的数据中心 |
| Windows Server 2012 R2 | 64 位 |  数据中心 |
| Windows Server 2012 | 64 位 |  数据中心 |
| Windows Server 2008 R2 SP1 | 64 位 |  数据中心 |
| Windows Server 2008 SP2 | 64 位 |  将你自己的映像 |
| Windows 10*（请参见注释 1）* | 64 位，Pro 和 Enterprise | 将你自己的映像 |

***注释 1:*** *要部署 Azure 堆栈上的 Windows 10 客户端操作系统，必须拥有[每用户许可 Windows](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx)或购买一个限定的多租户托管商通过 ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).*

应用商店映像是可用于为你的使用付费或 BYOL (EA/SPLA) 授权。 使用单个 Azure 堆栈实例上的不受支持。 在部署期间，Azure Stack 会将适当版本的来宾代理注入到映像中。

 Datacenter edition 中可用应用商店下载;客户可以将其自己的服务器映像包括其他版本。 Windows 客户端映像应用商店中不可用。

## <a name="linux"></a>Linux

在应用商店中列出为可用的 Linux 分发版包括必要 Windows Azure Linux 代理 (WALA)。 如果您为 Azure 堆栈提供你自己的映像，请按照中的准则[添加 Linux 映像分发到 Azure 堆栈](azure-stack-linux.md)。

> [!NOTE]
> 应使用最新的公共 WALA 版本生成的自定义映像。 版本早于 2.2.18 Azure 堆栈上可能无法正常工作。
>
> [cloud-init](https://cloud-init.io/)不支持 Azure 堆栈这次。

| 分发 | 说明 | 发布者 | Marketplace |
| --- | --- | --- | --- | --- | --- |
| 基于 CentOS 的 6.9 | 64 位 | Rogue Wave | 是 |
| 基于 CentOS 的 7.4 | 64 位 | Rogue Wave | 是 |
| ClearLinux | 64 位 | ClearLinux.org | 是 |
| 容器 Linux |  64 位 | CoreOS | Stable |
| Debian 8 "Jessie" | 64 位 | credativ |  是 |
| Debian 9“Stretch” | 64 位 | credativ | 是 |
| Red Hat Enterprise Linux 7.x | 64 位 | Red Hat |将你自己的映像 |
| SLES 11SP4 | 64 位 | SUSE | 是 |
| SLES 12SP3 | 64 位 | SUSE | 是 |
| Ubuntu 14.04-LTS | 64 位 | Canonical | 是 |
| Ubuntu 16.04-LTS | 64 位 | Canonical | 是 |
| Ubuntu 18.04 LTS | 64 位 | Canonical | 是 |

将来可能支持其他 Linux 发行版。

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
ms.date: 05/11/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: 8d9337053c8905886ed4429d64f8ef5b4e2c7d14
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060441"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Azure Stack 支持的来宾操作系统

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="windows"></a>Windows

Azure Stack 支持下表中列出的 Windows 来宾操作系统：

| 操作系统 | 说明 | 在市场中可用 |
| --- | --- | --- | --- | --- | --- |
| Windows Server，版本 1709 | 64 位 | 包含容器的核心 |
| Windows Server 2016 | 64 位 |  数据中心、数据中心核心、包含容器的数据中心 |
| Windows Server 2012 R2 | 64 位 |  数据中心 |
| Windows Server 2012 | 64 位 |  数据中心 |
| Windows Server 2008 R2 SP1 | 64 位 |  数据中心 |
| Windows Server 2008 SP2 | 64 位 |  自带映像 |
| Windows 10 *（请参见注释 1）* | 64 位，Pro 和 Enterprise | 自带映像 |

***注释 1:*** *要部署 Azure 堆栈上的 Windows 10 客户端操作系统，必须拥有[每用户许可 Windows](https://www.microsoft.com/en-us/Licensing/product-licensing/windows10.aspx)或购买一个限定的多租户托管商通过 ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).*

市场映像可用于即用即付或 BYOL (EA/SPLA) 授权。 不支持在单个 Azure Stack 实例上同时使用这两者。 在部署期间，Azure Stack 会将适当版本的来宾代理注入到映像中。

 Datacenter 版本可在市场中下载；客户可以自带包含其他版本的服务器映像。 市场中未提供 Windows 客户端映像。

## <a name="linux"></a>Linux

在应用商店中列出为可用的 Linux 分发版包括必要 Windows Azure Linux 代理 (WALA)。 如果将自己的映像带到 Azure Stack，请遵循[将 Linux 映像添加到 Azure Stack](azure-stack-linux.md) 中的指导。

> [!NOTE]
> 应使用最新的公共 WALA 版本生成自定义映像。 低于 2.2.18 的版本在 Azure Stack 上可能无法正常运行。
>
> 目前 Azure Stack 不支持 [cloud-init](https://cloud-init.io/)。

| 分发 | 说明 | 发布者 | 市场 |
| --- | --- | --- | --- | --- | --- |
| 基于 CentOS 的 6.9 | 64 位 | Rogue Wave | 是 |
| 基于 CentOS 的 7.4 | 64 位 | Rogue Wave | 是 |
| ClearLinux | 64 位 | ClearLinux.org | 是 |
| 容器 Linux |  64 位 | CoreOS | Stable |
| Debian 8 "Jessie" | 64 位 | credativ |  是 |
| Debian 9“Stretch” | 64 位 | credativ | 是 |
| Red Hat Enterprise Linux 7.x | 64 位 | Red Hat |自带映像 |
| SLES 11SP4 | 64 位 | SUSE | 是 |
| SLES 12SP3 | 64 位 | SUSE | 是 |
| Ubuntu 14.04-LTS | 64 位 | Canonical | 是 |
| Ubuntu 16.04-LTS | 64 位 | Canonical | 是 |
| Ubuntu 18.04-LTS | 64 位 | Canonical | 是 |

将来可能支持其他 Linux 发行版。

有关 Red Hat Enterprise Linux 支持信息，请参阅 [Red Hat 和 Azure Stack：常见问题解答](https://access.redhat.com/articles/3413531)。

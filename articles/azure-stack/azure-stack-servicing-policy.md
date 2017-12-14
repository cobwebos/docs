---
title: "维护服务策略的 azure 堆栈 |Microsoft 文档"
description: "了解有关 Azure 堆栈维护策略，以及如何在受支持的状态中保留一个集成的系统信息。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 498d0cdc3eac25b8efc7339e48381a4d167c0c7b
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="azure-stack-servicing-policy"></a>Azure 堆栈维护策略

*适用范围： Azure 堆栈集成系统*

本指南介绍了集成的 Azure 堆栈系统和必须执行哪些操作来使您的系统中受支持的状态的维护策略。 

## <a name="update-package-types"></a>更新包类型

有两种类型的集成系统; 的更新包Microsoft 软件更新和特定于你的原始设备制造商 (OEM) 硬件供应商联系，驱动程序和固件等的更新。 这些更新作为单独的 Azure 堆栈更新包，一起传递，并且独立进行管理。

- **Microsoft 软件更新**。 Microsoft 负责的 Microsoft 软件更新包的端到端维护服务生命周期。 这些包可以包括最新的 Windows Server 安全更新、 非安全更新和 Azure 堆栈功能更新。 你可以直接从 Microsoft 下载这些更新包。
- **OEM 硬件供应商提供更新**。 Azure 堆栈硬件合作伙伴负责的端到端维护服务生命周期 （包括指南） 与硬件相关的固件和驱动程序更新包。 此外，Azure 堆栈硬件合作伙伴始终拥有和维护的所有软件和硬件生命周期主机上的硬件的指南。 OEM 硬件供应商联系承载这些更新自己下载站点上的包。

## <a name="update-package-release-cadence"></a>更新包发布节奏

Microsoft 希望发布软件更新包以每月的频率。 但是，很可能有多个，或不在一个月的更新版本。 OEM 硬件供应商发布基于根据需要其更新。

Microsoft 更新包具有以下命名约定，以帮助你轻松地识别发布日期：

*MajorProductVersion.MinorProductVersion.YYMMDD.BuildNumber*

例如，在 2017 年 6 月 15 日发布的 Microsoft 软件更新将具有"1.0.170615.1"的版本。

## <a name="keep-your-system-under-support"></a>使您的系统受支持

若要接收你的系统的支持，则必须保持在特定时间间隔内更新你 Azure 堆栈。 我们对于延迟的 Microsoft 软件更新的策略是三个月。 如果你的系统已超过三个月内过期，你要视为不符合。 你必须更新系统到至少是支持的最低版本，以获得支持。 

Microsoft 软件更新包非累积的并且需要作为必备组件以前的更新包。 如果你决定将一个或多个更新延迟，请考虑整体运行时，如果你想要获取的最新版本。

下表显示示例更新包版本、 其先决条件和你的系统必须是在维护支持的最低支持的版本。 表基于在 2017 年 9 月的第一个更新包版本 (1709) 集成的 Azure 堆栈 （生成 1708年） 系统，初始版本。 

| 最新的更新包 (*示例*) | 先决条件 | 支持的最低版本 |
| -- | -- | -- |
| 1709 | 生成 1708 | 不适用 |
| 1710 | 1709 | 不适用 |
| 1711 | 1710 | 不适用 |
| 1712 | 1711 | 1709 |
| 1801 | 1712 | 1710 |
| 1802 | 1801 | 1711 |
| 1803 | 1802 | 1712 |
| 1804 | 1803 | 1801 |
| | | 

## <a name="next-steps"></a>后续步骤

- [管理 Azure 堆栈中的更新](azure-stack-updates.md)



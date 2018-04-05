---
title: Azure Stack 服务策略 | Microsoft Docs
description: 了解 Azure Stack 服务策略，以及如何使集成系统保持在受支持的状态。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2018
ms.author: mabrigg
ms.openlocfilehash: e37b63580d8cea4b5772bc54f7b2f79980afc0bc
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack 服务策略
本文介绍 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态。 

## <a name="update-package-types"></a>更新包类型

集成系统有两种类型的的更新包：Microsoft 软件更新，以及特定于原始设备制造商 (OEM) 硬件供应商的更新，例如驱动程序和固件。 这些更新是以单独的 Azure Stack 更新包提供，而且独立管理。

- **Microsoft 软件更新**。 Microsoft 会负责 Microsoft 软件更新包的端到端服务生命周期。 这些包可以包括最新的 Windows Server 安全更新、非安全更新和 Azure Stack 功能更新。 可以直接从 Microsoft 下载这些更新包。
- **OEM 硬件供应商提供的更新**。 Azure Stack 硬件合作伙伴负责硬件相关固件和驱动程序更新包的端到端服务生命周期（包括指导）。 此外，对于硬件生命周期主机上的所有软件和硬件，Azure Stack 硬件合作伙伴拥有并维护指导。 OEM 硬件供应商在自己的下载站点上托管这些更新包。

## <a name="update-package-release-cadence"></a>更新包发布频率

Microsoft 预期每月发布软件更新包。 但是，可能一个月内发布多个更新或没有任何更新。 OEM 硬件供应商会根据需要发布更新。

Microsoft 更新包具有以下命名约定，可帮助你轻松识别发布日期：

*MajorProductVersion.MinorProductVersion.YYMMDD.BuildNumber*

例如，2017 年 6 月 15 日发布的 Microsoft 软件更新是版本“1.0.170615.1”。

## <a name="keep-your-system-under-support"></a>保持系统受支持
若要继续接收支持，你必须保留 Azure 堆栈部署当前。 延迟的更新的策略是，对于 Azure 堆栈，以保留在支持，它必须运行的最近发布的更新版本或运行的两个前面的重大更新版本。  修补程序不会视为重大更新版本。  如果你的 Azure 堆栈云后通过*两个以上的更新*，它被视为不符合，必须将更新为至少支持的最低版本才能获得支持。 

例如，如果最近可用的更新版本为 1805，而以前的两个更新包是版本 1804年和 1803年，1803年和 1804年保留在支持。 但是，1802年不支持。 在一个月或两个没有任何版本时，策略保持为 true。 例如，如果当前版本是 1805年并且没有任何 1804年版本，1803年和 1802年以前的两个更新包将保持的支持。

Microsoft 软件更新包不是累积性，并且需要作为必备组件以前的更新包。 如果你决定将一个或多个更新延迟，请考虑整体运行时，如果你想要获取的最新版本。 


## <a name="next-steps"></a>后续步骤

- [在 Azure Stack 中管理更新](azure-stack-updates.md)



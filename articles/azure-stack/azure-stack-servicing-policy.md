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
ms.date: 04/09/2018
ms.author: brenduns
ms.reviewer: harik
ms.openlocfilehash: 160ba42c5cbdd3e8b999040cba8254d4c87f7c63
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack 服务策略
本文介绍 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态。 

## <a name="update-package-types"></a>更新包类型

有两种类型的集成系统的更新包： 

- **Microsoft 软件更新**。 Microsoft 会负责 Microsoft 软件更新包的端到端服务生命周期。 这些包可以包括最新的 Windows Server 安全更新、非安全更新和 Azure Stack 功能更新。 可以直接从 Microsoft 下载这些更新包。

- **OEM 硬件供应商提供的更新**。 Azure Stack 硬件合作伙伴负责硬件相关固件和驱动程序更新包的端到端服务生命周期（包括指导）。 此外，对于硬件生命周期主机上的所有软件和硬件，Azure Stack 硬件合作伙伴拥有并维护指导。 OEM 硬件供应商在自己的下载站点上托管这些更新包。


## <a name="update-package-release-cadence"></a>更新包发布频率
Microsoft 预期每月发布软件更新包。 但是，可能一个月内发布多个更新或没有任何更新。 OEM 硬件供应商会根据需要发布更新。 

查找有关如何规划和管理更新，以及如何确定在当前版本的文档[管理更新概述](azure-stack-updates.md)。 有关特定更新的信息，包括如何下载它，请参阅发行说明的更新： 
- [Azure 堆栈 1803年更新](azure-stack-update-1803.md)
- [Azure 堆栈 1802年更新](azure-stack-update-1802.md)
- [Azure 堆栈 1712年更新](azure-stack-update-1712.md)



## <a name="hotfixes"></a>修补程序
有时，Microsoft 提供的修补程序用于 Azure 堆栈该地址通常是预防性或具有时效性的特定问题。  每个修补程序将被释放，相应的 Microsoft 知识库文章，其中详细列出问题、 原因以及解决方法。 

修补程序下载并安装用于 Azure 堆栈一样的正则完整的更新包。 但是，与完整的更新，不同修补程序可以安装以分钟为单位。 我们建议安装修补程序时，Azure 堆栈运算符设置维护时段。 修补程序更新的新版 Azure 堆栈云使你可以轻松地确定是否已应用此修补程序。 仍在支持的 Azure 堆栈的每个版本提供单独的修补程序。 每个修补程序的特定迭代累积并包含相同的版本为以前的更新。 你可以阅读更多有关的相应知识库修补程序中的特定修补程序适用性文章。  


## <a name="keep-your-system-under-support"></a>保持系统受支持
必须不断更新 Azure Stack 部署才能持续获得支持。 更新延迟的策略是： 对于 Azure 堆栈部署，以便保留在支持，它必须运行的最近发布的更新版本或运行的两个前面的更新版本。 修补程序不属于主要更新版本。 如果你的 Azure 堆栈云后通过*两个以上的更新*，它被视为不符合，必须将更新为至少支持的最低版本才能获得支持。 

例如，如果最新发布的更新版本为 1805，在此之前的两个更新包为版本 1804 和 1803，则 1803 和 1804 仍受支持， 但 1802 不受支持。 即使最近一到两个月没有发布任何版本，此策略也有效。 例如，如果当前版本是 1805年并且没有任何 1804年版本，1803年和 1802年以前的两个更新包保持的支持。

Microsoft 软件更新包是非累积性的，其先决条件是需要前一个更新包。 如果决定延后一个或多个更新，则要使用最新版本，请考虑整体运行时。 


## <a name="next-steps"></a>后续步骤

- [在 Azure Stack 中管理更新](azure-stack-updates.md)



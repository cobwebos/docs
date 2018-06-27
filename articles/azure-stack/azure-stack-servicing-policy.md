---
title: Azure Stack 服务策略 | Microsoft Docs
description: 了解 Azure Stack 服务策略，以及如何使集成系统保持在受支持的状态。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: brenduns
ms.reviewer: harik
ms.openlocfilehash: c3cc8857373238079fee06c61faec962d7e3a6b2
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796412"
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack 服务策略
本文介绍 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态。 

## <a name="update-package-types"></a>更新包类型

集成系统有两种类型的更新包： 

- **Microsoft 软件更新**。 Microsoft 会负责 Microsoft 软件更新包的端到端服务生命周期。 这些包可以包括最新的 Windows Server 安全更新、非安全更新和 Azure Stack 功能更新。 可以直接从 Microsoft 下载这些更新包。

- **OEM 硬件供应商提供的更新**。 Azure Stack 硬件合作伙伴负责硬件相关固件和驱动程序更新包的端到端服务生命周期（包括指导）。 此外，对于硬件生命周期主机上的所有软件和硬件，Azure Stack 硬件合作伙伴拥有并维护指导。 OEM 硬件供应商在自己的下载站点上托管这些更新包。


## <a name="update-package-release-cadence"></a>更新包发布频率
Microsoft 预期每月发布软件更新包。 但是，可能一个月内发布多个更新或没有任何更新。 OEM 硬件供应商会根据需要发布更新。 

在[管理更新概述](azure-stack-updates.md)文档中，可以了解如何规划和管理更新，以及如何确定当前版本。 有关特定更新（包括其下载方法）的信息，请参阅该更新的发行说明： 
- [Azure 堆栈 1805年更新](azure-stack-update-1805.md)
- [Azure 堆栈 1804年更新](azure-stack-update-1804.md)
- [Azure Stack 1803 更新](azure-stack-update-1803.md)


## <a name="hotfixes"></a>修补程序
Microsoft 偶尔会提供 Azure Stack 的修补程序（通常是预防性或时效性的程序）来解决具体的问题。  发布的每个修补程序都附带相应的 Microsoft 知识库文章，其中详细描述了问题、原因和解决方法。 

可以像下载和安装普通的 Azure Stack 完整更新包一样下载和安装修补程序。 但是，与完整更新不同，修补程序在几分钟内即可完成安装。 我们建议 Azure Stack 操作员在安装修补程序时设置维护时段。 修补程序会更新 Azure Stack 云的版本，使你可以轻松确定是否已应用该修补程序。 对于仍在支持期内的每个 Azure Stack 版本，将单独提供修补程序。 特定迭代的每个修复程序是累积性的，包含同一版本的以往更新。 可以在相应的知识库文章中详细了解特定修补程序的适用性。  


## <a name="keep-your-system-under-support"></a>保持系统受支持
必须不断更新 Azure Stack 部署才能持续获得支持。 更新的延期策略是：为了确保 Azure Stack 部署始终获得支持，必须运行最近发布的更新版本，或者运行以前发布的两个更新版本之一。 修补程序不属于主要更新版本。 如果缺少至少两个更新，Azure Stack 云会被视为不合规，必须至少更新到最低的受支持版本才能获得支持。 

例如，如果最新发布的更新版本为 1805，在此之前的两个更新包为版本 1804 和 1803，则 1803 和 1804 仍受支持， 但 1802 不受支持。 即使最近一到两个月没有发布任何版本，此策略也有效。 例如，如果最新版本为 1805，但没有版本 1804，则此前的两个更新包（1803 和 1802）仍可获得支持。

Microsoft 软件更新包是非累积性的，其先决条件是需要前一个更新包。 如果决定延后一个或多个更新，则要使用最新版本，请考虑整体运行时。 

## <a name="get-support"></a>获取支持
Azure 堆栈遵循作为 Azure 在相同的支持流程。 企业客户可以按照中描述的过程[如何创建 Azure 支持请求](/azure/azure-supportability/how-to-create-azure-support-request)。 如果你是客户的云服务提供程序 (CSP)，请联系你的 CSP 以获得支持。  有关详细信息，请参阅 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。 


## <a name="next-steps"></a>后续步骤

- [在 Azure Stack 中管理更新](azure-stack-updates.md)



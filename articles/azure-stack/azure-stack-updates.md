---
title: 在 Azure Stack 中管理更新概述 | Microsoft Docs
description: 了解 Azure Stack 集成系统的更新管理。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9b0781f4-2cd5-4619-a9b1-59182b4a6e43
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mabrigg
ms.openlocfilehash: 484f04dc2ed523d3f979b0ba857c92f90d9d32ad
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215804"
---
# <a name="manage-updates-in-azure-stack-overview"></a>在 Azure Stack 中管理更新概述

*适用于：Azure Stack 集成系统*

Microsoft 更新包的 Azure Stack 集成系统通常发布围绕每个月的第四个星期二。 有关特定通知过程，以确保更新通知达到你的组织的信息，请咨询 OEM。 您还可以在此文档库中检查**概述** > **发行说明**有关处于活动状态的支持的版本信息。 

每次发布的 Microsoft 软件更新均打包为单个更新包。 Azure Stack 操作员，可以导入、 安装和监视安装进度的这些更新在管理员门户中的包。 

原始设备制造商 (OEM) 硬件供应商也会发布更新，例如驱动程序和固件更新。 而 OEM 硬件供应商的情况下，这些更新传送作为单独的包，它们将被导入、 安装，并且从 Microsoft 更新包导入、 安装和管理管理相同的方式更新包。

若要保持系统受支持，必须始终将 Azure Stack 更新为特定版本级别。 请务必查看 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。

> [!NOTE]
> 无法将 Azure Stack 更新包应用于 Azure Stack 开发工具包。 更新包专为集成系统所设计。 有关信息，请参阅[重新部署 ASDK](https://docs.microsoft.com/en-us/azure/azure-stack/asdk)。

## <a name="the-update-resource-provider"></a>更新资源提供程序

Azure Stack 包含协调 Microsoft 软件更新应用的更新资源提供程序。 此资源提供程序可确保在所有物理主机、Service Fabric 应用程序和运行时以及所有基础结构虚拟机及其关联的服务上应用更新。

当更新安装，你可以作为更新过程目标 （例如，物理主机和基础结构虚拟机） 的 Azure Stack 中的各种子系统查看高级状态。

## <a name="plan-for-updates"></a>规划更新

我们强烈建议通知用户任何维护操作，并且，在正常维护时段安排在非工作时间在可能的情况。 维护操作会影响租户工作负荷和门户操作。

## <a name="using-the-update-tile-to-manage-updates"></a>使用“更新”磁贴管理更新
在管理员门户中管理更新。 Azure Stack 操作员可以到仪表板中使用更新磁贴：

- 查看重要信息，例如当前版本。
- 安装更新，并监视进度。
- 查看以前安装的更新的更新历史记录。
 
## <a name="determine-the-current-version"></a>确定当前版本

“更新”磁贴会显示当前的 Azure Stack 版本。 可以在管理员门户中使用下列其中一种方法转到“更新”磁贴：

- 在仪表板上的“更新”磁贴中查看当前版本。
 
   ![默认仪表板上的“更新”磁贴](./media/azure-stack-updates/image1.png)
 
- 在“区域管理”磁贴上，单击区域名称。 在“更新”磁贴中查看当前版本。

## <a name="next-steps"></a>后续步骤

- [Azure Stack 服务策略](azure-stack-servicing-policy.md) 
- [Azure Stack 中的区域管理](azure-stack-region-management.md)     



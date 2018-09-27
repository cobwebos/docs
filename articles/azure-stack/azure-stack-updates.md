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
ms.date: 09/10/2018
ms.author: mabrigg
ms.openlocfilehash: 67f363d14489340755251369b422475032d1e671
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222490"
---
# <a name="manage-updates-in-azure-stack-overview"></a>在 Azure Stack 中管理更新概述

*适用于：Azure Stack 集成系统*

Azure Stack 集成系统的 Microsoft 更新包通常大约在每月的第四个星期二发布。 请向你的 OEM 咨询具体的通知流程以确保更新能够到达你的组织。 还可以查看位于“概述” > “发行说明”下的此文档库来了解有关处于有效支持状态的发行版的信息。 

每次发布的 Microsoft 软件更新均打包为单个更新包。 Azure Stack 操作员可以从管理员门户导入和安装这些更新包，并监视这些更新包的安装进度。 

原始设备制造商 (OEM) 硬件供应商也会发布更新，例如驱动程序和固件更新。 虽然这些更新是由 OEM 硬件供应商作为单独的包提供的，但它们采用与来自 Microsoft 更新的更新包相同的方式进行导入、安装和管理。

若要保持系统受支持，必须始终将 Azure Stack 更新为特定版本级别。 请务必查看 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。

> [!NOTE]
> 无法将 Azure Stack 更新包应用于 Azure Stack 开发工具包。 更新包专为集成系统所设计。 有关信息，请参阅[重新部署 ASDK](https://docs.microsoft.com/azure/azure-stack/asdk)。

## <a name="the-update-resource-provider"></a>更新资源提供程序

Azure Stack 包含协调 Microsoft 软件更新应用的更新资源提供程序。 此资源提供程序可确保在所有物理主机、Service Fabric 应用程序和运行时以及所有基础结构虚拟机及其关联的服务上应用更新。

当更新安装时，由于更新进程以 Azure Stack 中的各种子系统（例如，物理主机和基础结构虚拟机）为目标，因此你可以查看高级状态。

## <a name="plan-for-updates"></a>规划更新

我们强烈建议你向用户通知任何维护操作，并尽可能将正常维护时段安排在非工作时间。 维护操作可能会同时影响租户工作负荷和门户操作。


- 在开始安装此更新之前，请运行[Test-azurestack](azure-stack-diagnostic-test.md)具有以下参数来验证 Azure Stack 的状态并解决发现的任何操作问题，包括所有警告和失败。 另外，请查看活动警报，并解决所有需要采取措施的警报。  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>使用“更新”磁贴管理更新
从管理员门户中管理更新。 Azure Stack 操作员可以使用仪表板中的“更新”磁贴执行以下操作：

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



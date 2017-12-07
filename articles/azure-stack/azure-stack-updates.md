---
title: "管理 Azure 堆栈概述中的更新 |Microsoft 文档"
description: "了解 Azure 堆栈集成系统的更新管理。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 9b0781f4-2cd5-4619-a9b1-59182b4a6e43
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: f5c7810a88bee65cf22276082f72b18a7f9e983f
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2017
---
# <a name="manage-updates-in-azure-stack-overview"></a>管理 Azure 堆栈概述中的更新

*适用范围： Azure 堆栈集成系统*

Microsoft 将发布通常将不会在每月的第四个星期二上以正则频率的集成的 Azure 堆栈系统的更新包开始常规可用性。 向你的 OEM 询问有关其特定通知过程以确保你的组织的更新通知 reach 或此检查下 Concepts\Release notes\Integrated 系统发行说明，了解有关特定版本的详细信息。

每个版本的 Microsoft 软件更新捆绑作为单个更新包中。 作为 Azure 堆栈操作员，你可以轻松地导入、 安装和监视器的安装进度的这些更新在管理员门户中的包。 

原始设备制造商 (OEM) 硬件供应商还将发布更新，例如驱动程序和固件更新。 这些更新都作为单独的包提供 OEM 硬件供应商，并从 Microsoft 更新上单独进行管理。

若要使你的系统受支持，则必须保持更新到特定版本级别的 Azure 堆栈。 请确保您查看[维护策略的 Azure 堆栈](azure-stack-servicing-policy.md)。

> [!NOTE]
> 无法将 Azure 堆栈更新包应用于 Azure 堆栈开发工具包。 更新包专门用于集成的系统。

## <a name="the-update-resource-provider"></a>更新资源提供程序

Azure 堆栈包括安排的 Microsoft 软件更新应用程序的更新资源提供程序。 此资源提供程序可确保在所有物理主机，Service Fabric 应用程序和运行时，和基础结构的所有虚拟机和其关联的服务应用的更新。

更新安装，你可以轻松地查看作为更新过程目标 Azure 堆栈 （例如，物理主机和基础结构的虚拟机） 中的各种子系统的高级状态。

## <a name="plan-for-updates"></a>规划更新

我们强烈建议你通知用户的任何维护操作，并且你在非工作时间期间尽可能多地安排正常的维护时段。 租户工作负载和门户的操作，则可能会影响维护操作。

## <a name="using-the-update-tile-to-manage-updates"></a>使用的更新磁贴以管理更新
在管理员门户中管理更新是一个简单的过程。 Azure 堆栈运算符可以导航到将仪表板中的更新磁贴：

- 查看如的当前版本的重要信息。
- 安装更新，并监视进度。
- 查看以前安装的更新的更新历史记录。
 
## <a name="determine-the-current-version"></a>确定当前的版本

更新磁贴显示 Azure 堆栈的当前版本。 在管理员门户中使用以下方法之一可以获取指向更新磁贴：

- 在仪表板中，查看中的当前版本**更新**磁贴。
 
   ![上默认的仪表板磁贴的更新](./media/azure-stack-updates/image1.png)
 
- 上**区域管理**磁贴中，单击区域名称。 查看中的当前版本**更新**磁贴。

## <a name="next-steps"></a>后续步骤

- [Azure 堆栈维护策略](azure-stack-servicing-policy.md) 
- [Azure 堆栈中的区域管理](azure-stack-region-management.md)     



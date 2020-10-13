---
title: 将 Windows 虚拟桌面与 Azure NetApp 文件一起使用 |Microsoft Docs
description: 提供有关通过 Azure NetApp 文件部署 Windows 虚拟桌面的最佳实践指南和示例蓝图。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: 0cd1f6210fbdb74e3fd511150157dccca3e92dda
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932458"
---
# <a name="benefits-of-using-azure-netapp-files-with-windows-virtual-desktop"></a>将 Azure NetApp 文件与 Windows 虚拟桌面配合使用的好处 

本文提供了有关在 Azure NetApp 文件中部署 Windows 虚拟桌面 (WVD) 的最佳实践指南。

Azure NetApp 文件是来自 Azure 的高性能文件存储服务。 它可以提供高达 450000 IOPS 和秒毫秒的延迟，从而支持极大规模的 Windows 虚拟桌面部署。 在保持数据平面访问的同时，几乎无需暂停 IO 即可按需立即调整带宽和更改 Azure NetApp 文件卷的服务级别。 此功能可让你轻松优化 WVD 部署规模。 你还可以创建节省空间的时间点卷快照，而不会影响卷性能。 此功能使你可以通过从目录中的副本回滚单个 [FSLogix 用户配置文件容器](../virtual-desktop/store-fslogix-profile.md) `~snapshot` ，或通过卷还原功能立即回滚整个卷。  使用最多 255 (旋转) 的快照，以防止数据丢失或损坏，管理员有很多机会撤消所做的工作。

## <a name="sample-blueprints"></a>示例蓝图

以下示例蓝图显示了 Windows 虚拟桌面与 Azure NetApp 文件的集成。 在共用桌面方案中，用户使用[多会话虚拟机](../virtual-desktop/windows-10-multisession-faq.md#what-is-windows-10-enterprise-multi-session)将 (定向到[池中) 主机](../virtual-desktop/host-pool-load-balancing.md#breadth-first-load-balancing-method)的最佳可用会话。 另一方面，在每个用户都有自己的虚拟机的情况下，将保留个人桌面。

### <a name="pooled-desktop-scenario"></a>共用桌面方案

对于共用方案，Windows 虚拟桌面团队按用户计数向 vCPU [建议](/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations) 以下指导。 请注意，在此建议中未指定虚拟机大小。

|     工作负荷类型     |     亮    |     中型    |     重型    |
|-----------------------|--------------|---------------|--------------|
|     用户/vCPU    |     6        |     4         |     2        |


此建议由 500-user LoginVSI test 确认，将大约 62 "知识/中型用户" 记录到每个 D16as_V4 虚拟机上。 

例如，在每个 D16as_V4 的虚拟机上，每个虚拟机62用户，Azure NetApp 文件可以轻松地支持每个环境60000个用户。 测试以评估 D32as_v4 虚拟机的上限。 如果 D32as_v4 的 WVD 用户按 vCPU 建议为 true，则在 broaching [1000 IP VNet 限制](./azure-netapp-files-network-topologies.md)之前，超过120000个用户将适合1000个虚拟机，如下图所示。  

![Windows 虚拟桌面共用桌面方案](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

### <a name="personal-desktop-scenario"></a>个人桌面方案 

在个人桌面方案中，下图显示了一般用途的体系结构建议。 用户映射到特定的桌面箱，每个 pod 只包含1000个虚拟机，并为从管理 VNet 传播的 IP 地址留出空间。 Azure NetApp 文件可轻松处理每个单会话主机池 VNet 的900多个个人桌面，而虚拟机的实际数量将等于1000减中心 VNet 中的管理主机数。 如果需要更多的个人桌面，可以轻松地将更多箱添加 (主机池和虚拟网络) 中，如下图所示。 

![Windows 虚拟桌面个人桌面方案](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

构建基于 POD 的体系结构时，将用户分配到登录时的正确 POD 是确保用户始终会找到其用户配置文件的重要信息。 

## <a name="next-steps"></a>后续步骤

- [使用 Azure NetApp 文件的解决方案体系结构](azure-netapp-files-solution-architectures.md)
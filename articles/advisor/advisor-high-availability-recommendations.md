---
title: "Azure 顾问高可用性建议 | Microsoft Docs"
description: "使用 Azure 顾问提高 Azure 部署的高可用性。"
services: advisor
documentationcenter: NA
author: kumudd
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: a560aa9cc6c70fd3827f649627466c9071b8ab8b
ms.openlocfilehash: 91b6fec93d4226af755be553e544a4ea7b1081ca

---

# <a name="advisor-high-availability-recommendations"></a>顾问高可用性建议

顾问可帮助确保并提高业务关键应用程序的连续性。 可以通过顾问仪表板的“高可用性”选项卡获取顾问的高可用性建议。

![](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="virtual-machines-without-an-availability-set"></a>没有可用性集的虚拟机

顾问标识不属于可用性集的虚拟机，并建议将它们移动到可用性集中。 若要为应用程序提供冗余，建议你将两个或更多虚拟机组合到一个可用性集中。 这种配置可以确保在计划内或计划外维护事件期间，至少有一个虚拟机可用，并满足 Azure 虚拟机 SLA 要求。 可以选择为虚拟机创建可用性集，或将虚拟机添加到现有可用性集。

> [!NOTE]
> 如果选择创建可用性集，则必须在创建可用性集后至少再向其中添加一台虚拟机。 建议在可用性集中对两个或多个虚拟机进行分组，确保其中一台虚拟机在出现故障期间可用。

![](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="availability-sets-with-a-single-virtual-machine"></a>含有单个虚拟机的可用性集 

顾问标识包含单个虚拟机的可用性集，并建议向其中添加一个或多个虚拟机。 若要为应用程序提供冗余，建议你将两个或更多虚拟机组合到一个可用性集中。 这种配置可以确保在计划内或计划外维护事件期间，至少有一个虚拟机可用，并满足 Azure 虚拟机 SLA 要求。 可以选择创建虚拟机或使用现有虚拟机，并将其添加到可用性集。  


![](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)

## <a name="virtual-machines-with-standard-disks"></a>带标准磁盘的虚拟机

顾问标识带标准磁盘的虚拟机，并建议升级为高级磁盘。  
Azure 高级存储为运行 I/O 密集型工作负荷的虚拟机提供高性能、低延迟的磁盘支持。 在固态硬盘 (SSD) 上使用高级存储存储数据的虚拟机磁盘。 建议将任何需要高 IOPS 的虚拟机磁盘迁移到 Azure 高级存储，以便应用程序实现最佳性能。 如果磁盘不需要高 IOPS，则可以通过将其保留在标准存储中来限制成本。 标准存储将虚拟机磁盘数据存储在硬盘驱动器 (HDD) 而不是 SSD 上。 可以选择将虚拟机磁盘迁移到高级磁盘。 大多数虚拟机 SKU 支持高级磁盘。 但是在某些情况下，如果要使用高级磁盘，可能也需要升级虚拟机 SKU。   

![](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png) 

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>如何访问顾问中的高可用性建议

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧导航窗格中，单击“更多服务”，在服务菜单窗格中，向下滚动到“监视和管理”，然后单击“Azure Advisor”。 这将启动顾问仪表板。 
3. 在顾问仪表板上，单击“高可用性”选项卡，然后选择要接收建议的订阅。

> [!NOTE]
> 如果订阅中已为用户分配了**所有者、参与者或读者**角色，则顾问会为其生成建议。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：
-  [Azure 顾问简介](advisor-overview.md)
-  [顾问入门](advisor-get-started.md)
-  [顾问安全性建议](advisor-security-recommendations.md)
-  [顾问性能建议](advisor-performance-recommendations.md)
-  [顾问成本建议](advisor-performance-recommendations.md)



<!--HONumber=Nov16_HO3-->



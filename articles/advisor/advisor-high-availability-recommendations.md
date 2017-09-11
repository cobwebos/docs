---
title: "Azure 顾问高可用性建议 | Microsoft Docs"
description: "使用 Azure 顾问提高 Azure 部署的高可用性。"
services: advisor
documentationcenter: NA
author: KumudD
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 23c159471a6e5a7ad9cb545840e8afd3ac72ecba
ms.contentlocale: zh-cn
ms.lasthandoff: 03/31/2017

---

# <a name="advisor-high-availability-recommendations"></a>顾问高可用性建议

Azure 顾问可帮助确保并提高业务关键应用程序的连续性。 可以通过顾问仪表板的“高可用性”选项卡获取顾问的高可用性建议。

![顾问仪表板上的“高可用性”按钮](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="ensure-virtual-machine-fault-tolerance"></a>确保虚拟机容错

顾问标识不属于可用性集的虚拟机，并建议将它们移动到可用性集中。 若要为应用程序提供冗余，建议你将两个或更多虚拟机组合到一个可用性集中。 这种配置可以确保在计划内或计划外维护事件期间，至少有一个虚拟机可用，并满足 Azure 虚拟机 SLA 要求。 可以选择为虚拟机创建可用性集，或将虚拟机添加到现有可用性集。

> [!NOTE]
> 如果选择创建可用性集，则必须至少再向其中添加一台虚拟机。 建议在可用性集中对两个或更多虚拟机进行分组，确保其中一台虚拟机在出现故障期间可用。

![顾问建议：对于虚拟机冗余，请使用可用性集](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="ensure-availability-set-fault-tolerance"></a>确保可用性集容错 

顾问标识包含单个虚拟机的可用性集，并建议向其中添加一个或多个虚拟机。 若要为应用程序提供冗余，建议你将两个或更多虚拟机组合到一个可用性集中。 这种配置可以确保在计划内或计划外维护事件期间，至少有一个虚拟机可用，并满足 Azure 虚拟机 SLA 要求。 可以选择创建虚拟机或使用现有虚拟机，并将其添加到可用性集。  

![顾问建议：将一个或多个 VM 添加到此可用性集](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)


## <a name="ensure-application-gateway-fault-tolerance"></a>确保应用程序网关容错
为了确保由应用程序网关提供支持的任务关键型应用程序的业务连续性，顾问会标识没有针对容错进行配置的应用程序网关实例，并建议可以执行的修正操作。 顾问会标识中型或大型单实例应用程序网关，并建议至少再添加一个实例。 它还标识单实例或多实例小型应用程序网关，并建议迁移到中型或大型 SKU。 顾问建议执行这些操作以确保应用程序网关实例配置为满足这些资源的当前 SLA 要求。

![顾问建议：部署两个或更多中型或大型应用程序网关实例](./media/advisor-high-availability-recommendations/advisor-high-availability-application-gateway.png)

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>提高虚拟机磁盘的性能和可靠性

顾问标识带标准磁盘的虚拟机，并建议升级为高级磁盘。
 
Azure 高级存储为运行 I/O 密集型工作负荷的虚拟机提供高性能、低延迟的磁盘支持。 在固态硬盘 (SSD) 上使用高级存储帐户存储数据的虚拟机磁盘。 为使应用程序实现最佳性能，建议将任何需要高 IOPS 的虚拟机磁盘迁移到高级存储。 

如果磁盘不需要高 IOPS，则可以通过将其保留在标准存储中来限制成本。 标准存储将虚拟机磁盘数据存储在硬盘驱动器 (HDD) 而不是 SSD 上。 可以选择将虚拟机磁盘迁移到高级磁盘。 大多数虚拟机 SKU 支持高级磁盘。 但是在某些情况下，如果要使用高级磁盘，可能也需要升级虚拟机 SKU。

![顾问建议：通过升级到高级磁盘来提高虚拟机磁盘的可靠性](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png)

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>防止意外删除虚拟机数据
顾问标识其中未启用备份的虚拟机，并建议启用备份。 设置虚拟机备份可确保业务关键型数据的可用性，并防止意外删除或损坏。

![顾问建议：配置虚拟机备份以保护任务关键型数据](./media/advisor-high-availability-recommendations/advisor-high-availability-virtual-machine-backup.png)

## <a name="access-high-availability-recommendations-in-advisor"></a>访问顾问中的高可用性建议

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在左窗格中，单击“更多服务”。

3. 在服务菜单窗格中，在“监视和管理”下，单击“Azure 顾问”。  
 将显示顾问仪表板。

4. 在顾问仪表板上，单击“高可用性”选项卡，然后选择要接收建议的订阅。

> [!NOTE]
> 若要访问顾问建议，必须先向顾问*注册订阅*。 订阅所有者启动顾问仪表板并单击“获取建议”按钮，即可注册订阅。 这是*一次性操作*。 注册订阅后，可通过订阅、资源组或特定资源的所有者、参与者或读者身份访问顾问建议。

## <a name="next-steps"></a>后续步骤

有关顾问建议的详细信息，请参阅以下资源：
* [Azure 顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-performance-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)



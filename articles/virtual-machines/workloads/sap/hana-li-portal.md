---
title: 通过 Azure 门户的 Azure HANA 大型实例控制 |Microsoft Docs
description: 介绍如何通过门户标识和交互 Azure HANA 大型实例
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2d64fe6c244ffcb6da2926dfea6efaa6da315727
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234458"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>通过 Azure 门户控制 Azure HANA 大型实例
本文档介绍了如何在[Azure 门户](https://portal.azure.com)中提供[HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), 以及可通过部署的 hana 大型实例单元 Azure 门户进行哪些活动。 Azure 门户中 HANA 大型实例的可见性是通过适用于 HANA 大型实例的 Azure 资源提供程序提供的, 当前正在公开预览

## <a name="register-hana-large-instance-resource-provider"></a>注册 HANA 大型实例资源提供程序
通常, 为 hana 大型实例资源提供程序注册使用的 Azure 订阅。 但是, 如果你看不到部署 HANA 大型实例单元, 则应在 Azure 订阅中注册资源提供程序。 注册 HANA 大型实例资源提供程序有两种方式

### <a name="register-through-cli-interface"></a>通过 CLI 接口注册
你需要登录到 Azure 订阅, 该订阅适用于 HANA 大型实例部署, 通过 Azure CLI 接口。 可以通过以下命令 (重新) 注册 HANA 大型实例提供程序:
    
    az provider register --namespace Microsoft.HanaOnAzure

有关详细信息, 请参阅[Azure 资源提供程序和类型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)一文


### <a name="register-through-azure-portal"></a>注册 Azure 门户
可以通过 Azure 门户 (重新) 注册 HANA 大型实例资源提供程序。 你需要在 Azure 门户中列出你的订阅, 然后双击用于部署 HANA 大型实例单元的订阅。 在订阅的 "概述" 页中, 选择 "资源提供程序" (如下所示), 并在 "搜索" 窗口中键入 "HANA"。 

![通过 Azure 门户注册 B-HLI RP](./media/hana-li-portal/portal-register-hli-rp.png)

在显示的屏幕截图中, 资源提供程序已注册。 如果尚未注册资源提供程序, 请按 "重新注册" 或 "注册"。

有关详细信息, 请参阅[Azure 资源提供程序和类型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)一文


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Azure 门户中的 HANA 大型实例单元的显示
提交 HANA 大型实例部署请求时, 需要指定要连接到 HANA 大型实例的 Azure 订阅。 建议使用与用于部署适用于 HANA 大型实例单位的 SAP 应用程序层的同一订阅。
随着第一个 HANA 大型实例的部署, 将在部署请求中为 HANA 大型实例提交的 Azure 订阅中创建新的[azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)。  新资源组将列出你在特定订阅中部署的所有 HANA 大型实例单元。

若要查找新的 Azure 资源组, 请通过在 Azure 门户的左侧导航窗格中导航来列出订阅中的资源组。

![Azure 门户中的导航窗格](./media/hana-li-portal/portal-resource-group.png)

在要列出的资源组列表中, 你可能需要筛选用于部署 HANA 大型实例的订阅

![在 Azure 门户中筛选资源组](./media/hana-li-portal/portal-filtering-subscription.png)

筛选到正确的订阅后, 仍可能会有一个较长的资源组列表。 查找一个具有 **-Txxx**的后修复, 其中 "xxx" 是三个数字, 如 **-T050**。 

找到资源组后, 列出它的详细信息。 收到的列表如下所示:

![Azure 门户中的 B-HLI 列表](./media/hana-li-portal/portal-hli-units-list.png)

列出的所有单位都表示已在订阅中部署的单个 HANA 大型实例单元。 在这种情况下, 你会看到8个不同的 HANA 大型实例单元, 这些单元已部署在你的订阅中。

如果在同一 Azure 订阅下部署了多个 HANA 大型实例租户, 会发现多个 Azure 资源组 


## <a name="look-at-attributes-of-single-hli-unit"></a>查看单个 B-HLI 单元的属性
在 HANA 大型实例单元的列表中, 可以单击单个单元, 然后转到单个 HANA 大型实例单元的详细信息。 

在 "概述" 屏幕中, 单击 "显示更多" 后, 你将看到如下所示的单元:

![显示 B-HLI 单位的概述](./media/hana-li-portal/portal-show-overview.png)

查看显示的不同属性, 这些属性看起来不像 Azure VM 属性。 在左侧标题中, 会显示资源组、Azure 区域、订阅名称和 ID 以及添加的一些标记。 默认情况下, HANA 大型实例单元未分配任何标记。 在标头的右侧, 在完成部署后, 该单元的名称将列出为 "已分配"。 显示操作系统以及 IP 地址。 与 Vm 一样, 还会显示具有 CPU 线程数和内存的 HANA 大型实例单元类型。 有关不同 HANA 大型实例单元的更多详细信息, 请参阅:

- [对 HLI 可用的 SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [SAP HANA (大型实例) 存储体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

右侧的其他数据是 HANA 大型实例标记的修订版本。 可能的值包括：

- 修订版3
- 修订版4

修订版4是 HANA 大型实例发布的最新体系结构, 它对 Azure Vm 和在修订版4标记或行中部署的 HANA 大型实例单元之间的网络延迟进行了重大改进。
"概述" 的右下角有另一个非常重要的信息, 其中包含为每个已部署 HANA 大型实例单元自动创建的 Azure 邻近位置组的名称。 部署托管 SAP 应用程序层的 Azure Vm 时, 需要引用此邻近的放置组。 通过使用与 HANA 大型实例单元关联的[azure 邻近性放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location), 确保将 azure vm 部署到与 Hana 大型实例单元密切接近的位置。 [有关 sap 应用程序的最佳网络延迟的 Azure 邻近性放置组中介绍了如何使用近程放置组查找同一 azure 数据中心内的 SAP 应用程序层](sap-proximity-placement-scenarios.md).

标头右侧列中的其他字段会通知 HANA 大型实例单元的电源状态。

> [!NOTE]
> 电源状态描述硬件设备是开机还是关机。 它不会给出有关正在启动并运行的操作系统的信息。 当您重新启动 HANA 大型实例单元时, 您将会遇到一小段时间, 其中, 单元状态更改为 "**正在**启动"**状态。** 处于 "**已启动**" 状态表示 os 正在启动, 或者操作系统已完全启动。 因此, 在重新启动该单元后, 一旦状态切换为 "**已启动**", 就无法立即登录到该设备。
> 

如果按 "查看更多", 则会显示附加信息。 另外, 还显示了在中部署的 HANA 大型实例标记的修订版本。 请参阅文章[Azure 上的什么是 Azure 上的 SAP HANA (大型实例)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) , 以了解 HANA 大型实例戳记的不同版本

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>检查单个 HANA 大型实例单元的活动 
除了提供 HANA 大型实例单位的概述外, 还可以检查特定单位的活动。 活动日志如下所示:

![Azure 门户中的导航窗格](./media/hana-li-portal/portal-activity-list.png)

记录的主要活动之一是重新启动单位。 列出的数据包括活动状态、触发活动的时间戳、触发活动的订阅 ID, 以及触发活动的 Azure 用户。 

另一个正在记录的活动是对 Azure 元数据中的单元的更改。 除了启动重新启动之外, 你还可以看到**Write HANAInstances**的活动。 这种类型的活动不会在 HANA 大型实例单元本身上执行任何更改, 而是记录对 Azure 中单元的元数据的更改。 在列出的情况下, 我们添加并删除了标记 (请参阅下一部分)。

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>向 HANA 大型实例单元添加和删除 Azure 标记
另一种可能性是将[标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)添加到 HANA 大型实例单元。 获取标记的方式与将标记分配给 Vm 的方式不同。 与 Vm 一样, Azure 元数据中存在标记, 对于 HANA 大型实例, 与 Vm 的标记具有相同的限制。

删除标记的工作方式与使用 Vm 的方式相同。 应用和删除标记的两个活动都将列在特定 HANA 大型实例单元的活动日志中。

## <a name="check-properties-of-a-hana-large-instance-unit"></a>检查 HANA 大型实例单元的属性
节**属性**包含你在将实例移交给你时获得的重要信息。 这是一个部分, 你可以在其中获取支持案例中所需的所有信息, 或设置存储快照配置时所需的信息。 此部分是围绕你的实例的数据集合、实例与 Azure 的连接以及存储后端。 该部分的顶部如下所示:


![Azure 门户中的 B-HLI 属性的上半部分](./media/hana-li-portal/portal-properties-top.png)

前几个数据项已经在概述屏幕上看到。 但数据的一个重要部分是 ExpressRoute 线路 ID, 你作为首个部署单元的提交目标。 在某些支持情况下, 可能会要求您输入该数据。 屏幕截图底部显示了重要的数据输入。 显示的数据是将存储隔离到 HANA 大型实例堆栈中的**租户**的 NFS 存储头的 IP 地址。 编辑[存储快照备份的配置文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots)时, 也需要此 IP 地址。 

当你在 "属性" 窗格中向下滚动时, 将获取 HANA 大型实例单元的唯一资源 ID 或分配给部署的订阅 ID 等其他数据。

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>通过 Azure 门户重启 HANA 大型实例单元
开始重新启动 Linux 操作系统时, 操作系统无法成功完成重新启动。 若要强制重新启动, 需要打开服务请求, 让 Microsoft 操作执行 HANA 大型实例单元的电源重启。 HANA 大型实例单元的电源重新启动功能现在已集成到 Azure 门户中。 在 HANA 大型实例单元的 "概述" 部分中, 你会看到 "在数据" 部分顶部重新启动的按钮

![重新启动 Azure 门户中的步骤 #1](./media/hana-li-portal/portal-restart-first-step.png)

按下 "重新启动" 按钮时, 系统会询问你是否确实要重新启动该单元。 按下按钮 "是" 进行确认时, 设备将重新启动。

> [!NOTE]
> 在重新启动过程中, 将出现一小段时间, 其中单元状态更改为 "**正在**启动 **"。** 处于 "**已启动**" 状态表示 os 正在启动, 或者操作系统已完全启动。 因此, 在重新启动该单元后, 一旦状态切换为 "**已启动**", 就无法立即登录到该设备。

> [!IMPORTANT]
> 取决于 HANA 大型实例单元中的内存量, 重新启动和重新启动硬件和操作系统最多可能需要一小时


## <a name="open-a-support-request-for-hana-large-instances"></a>为 HANA 大型实例打开支持请求
除了 Azure 门户显示 HANA 大型实例单元外, 还可以创建特别适用于 HANA 大型实例单元的支持请求。 按照链接**新的支持请求**进行操作 

![在 Azure 门户中启动服务请求步骤 #1](./media/hana-li-portal/portal-initiate-support-request.png)

为了获取下一屏幕中列出的 SAP HANA 大型实例服务, 可能需要选择 "所有服务", 如下所示

![在 Azure 门户中选择 "所有服务"](./media/hana-li-portal/portal-create-service-request.png)

在服务列表中, 可以找到服务**SAP HANA 大型实例**。 选择该服务时, 可以选择特定的问题类型, 如下所示:


![在 Azure 门户中选择问题类](./media/hana-li-portal/portal-select-problem-class.png)

在各种不同的问题类型下, 将提供所需选择的问题子类型, 以便进一步描述问题。 选择子类型后, 现在可以命名该主题。 完成选择过程后, 就可以转到创建的下一步。 在 "**解决方案**" 部分中, 你将指向有关 HANA 大型实例的文档, 这些实例可能会给出问题的解决方案的一个指针。 如果在建议的文档中找不到问题的解决方案, 请执行下一步。 在下一步中, 系统将询问你问题是与 Vm 一起还是与 HANA 大型实例单元一起出现。 此信息可帮助将支持请求定向到正确的专家。 

![Azure 门户中的支持案例的详细信息](./media/hana-li-portal/portal-support-request-details.png)

当你回答了这些问题并提供了其他详细信息后, 你可以执行下一步, 以便查看支持请求并提交。

## <a name="next-steps"></a>后续步骤

- [如何在 Azure 上监视 SAP HANA (大型实例)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [HANA 端的监视和故障排除](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)


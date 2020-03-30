---
title: Azure HANA 大型实例通过 Azure 门户控制 |微软文档
description: 描述如何通过门户识别和与 Azure HANA 大型实例进行交互
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c14ff9c4f6d2bc2b1a62d1874d01950d09491c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "70099817"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>通过 Azure 门户控制 Azure HANA 大型实例
本文档介绍了在[Azure 门户](https://portal.azure.com)中显示[HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)的方式，以及哪些活动可以通过 Azure 门户进行，其中部署了 HANA 大型实例单元。 Azure 门户中 HANA 大型实例的可见性通过 HANA 大型实例的 Azure 资源提供程序提供，该提供程序当前处于公共预览版中

## <a name="register-hana-large-instance-resource-provider"></a>注册 HANA 大型实例资源提供程序
通常，用于 HANA 大型实例部署的 Azure 订阅为 HANA 大型实例资源提供程序注册。 但是，如果您看不到已部署的 HANA 大型实例单元，则应在 Azure 订阅中注册资源提供程序。 注册 HANA 大型实例资源提供程序有两种方法

### <a name="register-through-cli-interface"></a>通过 CLI 界面注册
您需要登录到 Azure 订阅，该订阅通过 Azure CLI 接口用于 HANA 大型实例部署。 您可以使用以下命令（重新）注册 HANA 大型实例提供程序：
    
    az provider register --namespace Microsoft.HanaOnAzure

有关详细信息，请参阅 Azure[资源提供程序和类型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)的文章


### <a name="register-through-azure-portal"></a>通过 Azure 门户注册
您可以通过 Azure 门户（重新）注册 HANA 大型实例资源提供程序。 您需要在 Azure 门户中列出订阅，并双击订阅，该订阅用于部署 HANA 大型实例单元。 在订阅的概述页中，选择如下所示的"资源提供程序"，并在搜索窗口中键入"HANA"。 

![通过 Azure 门户注册 HLI RP](./media/hana-li-portal/portal-register-hli-rp.png)

在显示的屏幕截图中，资源提供程序已注册。 如果资源提供程序尚未注册，请按"重新注册"或"注册"。

有关详细信息，请参阅 Azure[资源提供程序和类型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)的文章


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>在 Azure 门户中显示 HANA 大型实例单元
提交 HANA 大型实例部署请求时，系统将要求您指定要连接到 HANA 大型实例的 Azure 订阅。 建议使用与您使用的相同订阅来部署针对 HANA 大型实例单元的 SAP 应用程序层。
部署第一个 HANA 大型实例时，将在您在 HANA 大型实例的部署请求中提交的 Azure 订阅中创建新的[Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)。  新资源组将列出在特定订阅中部署的所有 HANA 大型实例单元。

为了查找新的 Azure 资源组，可以通过浏览 Azure 门户的左侧导航窗格来列出订阅中的资源组

![Azure 门户中的导航窗格](./media/hana-li-portal/portal-resource-group.png)

在资源组列表中，您要列出，您可能需要筛选以前部署 HANA 大型实例的订阅

![在 Azure 门户中筛选资源组](./media/hana-li-portal/portal-filtering-subscription.png)

筛选到正确的订阅后，您仍可能有一长串的资源组。 查找一个后修复 **-Txxx，"xxx"** 是三位数字，如 **-T050**。 

找到资源组时，请列出资源组的详细信息。 您收到的列表可能如下所示：

![Azure 门户中的 HLI 列表](./media/hana-li-portal/portal-hli-units-list.png)

列出的所有单元都表示已在订阅中部署的单个 HANA 大型实例单元。 在这种情况下，您查看了 8 个不同的 HANA 大型实例单元，这些单元已部署在订阅中。

如果在同一 Azure 订阅下部署了多个 HANA 大型实例租户，则会发现多个 Azure 资源组 


## <a name="look-at-attributes-of-single-hli-unit"></a>查看单个 HLI 单元的属性
在 HANA 大型实例单元列表中，您可以单击单个单元并获取单个 HANA 大型实例单元的详细信息。 

在概述屏幕中，单击"显示更多"后，您会收到该装置的演示文稿，如下所示：

![显示 HLI 单元的概述](./media/hana-li-portal/portal-show-overview.png)

查看显示的不同属性，这些属性看起来与 Azure VM 属性几乎没什么不同。 在左侧标题上，它显示资源组、Azure 区域、订阅名称和 ID 以及添加的某些标记。 默认情况下，HANA 大型实例单元没有分配标记。 在标头的右侧，单元的名称在部署完成后列为已分配的名称。 操作系统以及 IP 地址都显示。 与 VM 一样，也显示了具有 CPU 线程数和内存的 HANA 大型实例单元类型。 有关不同 HANA 大型实例单元的更多详细信息，如下所示：

- [对 HLI 可用的 SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [SAP HANA（大型实例）存储体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

右侧的其他数据是 HANA 大型实例戳的修订版。 可能的值包括：

- 修订版 3
- 修订版 4

修订版 4 是 HANA 大型实例发布的最新体系结构，在 Azure VM 和 HANA 大型实例单元之间的网络延迟方面有重大改进，部署在版本 4 戳或行中。
另一个非常重要的信息位于概览的右下角，其名称为 Azure 邻近放置组，该组会自动为每个已部署的 HANA 大型实例单元创建。 在部署托管 SAP 应用程序层的 Azure VM 时，需要引用此邻近放置组。 通过使用与 HANA 大型实例单元关联的[Azure 邻近放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)，可以确保 Azure VM 部署在 HANA 大型实例单元附近。 Azure 邻近放置组中介绍了如何使用邻近放置组查找与修订版 4 托管 HANA 大型实例单元位于同一 Azure 数据中心中的 SAP 应用程序层的方式，[以便使用 SAP 应用程序实现最佳网络延迟](sap-proximity-placement-scenarios.md)。

标题右列中的附加字段将通知 HANA 大型实例单元的电源状态。

> [!NOTE]
> 电源状态描述硬件单元是开机还是关。 它不提供有关操作系统正在启动和运行的信息。 重新启动 HANA 大型实例单元时，您会遇到一小段时间，设备的状态更改为 **"开始**"进入 **"已启动"** 状态。 处于 **"已启动"** 状态意味着操作系统正在启动或操作系统已完全启动。 因此，重新启动设备后，您不能指望在状态切换到 **"已启动"** 时立即登录到设备。
> 

如果按"查看详细信息"，将显示其他信息。 另外一个信息是显示 HANA 大型实例戳的修订版，该版本已部署在其中。 有关 HANA 大型实例图章的不同修订版本，请参阅[Azure（大型实例）上的 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)文章

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>检查单个 HANA 大型实例单元的活动 
除了概述 HANA 大型实例单元外，您还可以检查特定单元的活动。 活动日志可能如下所示：

![Azure 门户中的导航窗格](./media/hana-li-portal/portal-activity-list.png)

记录的主要活动之一是重新启动设备。 列出的数据包括活动的状态、触发活动的时间戳、触发活动的订阅 ID 以及触发活动的 Azure 用户。 

正在记录的另一个活动是 Azure 元数据中单元的更改。 除了重新启动启动之外，您还可以看到**写入 HANA实例**的活动。 这种类型的活动对 HANA 大型实例单元本身不执行任何更改，而是记录对 Azure 中单元的元数据的更改。 在列出的案例中，我们添加并删除了一个标记（请参阅下一节）。

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>向 HANA 大型实例单元添加和删除 Azure 标记
另一种可能性是向 HANA 大型实例单元添加[标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)。 标记的分配方式与向 VM 分配标记的方式不同。 与 VM 一样，标记存在于 Azure 元数据中，对于 HANA 大型实例，标记具有与 VM 标记相同的限制。

删除标记的工作方式与 VM 相同。 应用和删除标记的活动都将列在特定 HANA 大型实例单元的活动日志中。

## <a name="check-properties-of-a-hana-large-instance-unit"></a>检查 HANA 大型实例单元的属性
**属性**部分包括将实例移交给您时获取的重要信息。 在其中，您可以获取在支持情况下可能需要的所有信息，或者在设置存储快照配置时需要的信息。 因此，本节是实例周围的数据集合、实例与 Azure 的连接以及存储后端。 该部分的顶部如下所示：


![Azure 门户中 HLI 属性的顶部部分](./media/hana-li-portal/portal-properties-top.png)

在概述屏幕中已经看到的前几个数据项。 但数据的一个重要部分是 ExpressRoute 电路 ID，当第一个部署的单元被移交时，您得到了该 ID。 在某些支持情况下，可能会要求您提供该数据。 一个重要的数据条目显示在屏幕截图的底部。 显示的数据是 NFS 存储头的 IP 地址，该地址将存储隔离到 HANA 大型实例堆栈中的**租户**。 当您编辑[存储快照备份的配置文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots)时，也需要此 IP 地址。 

在属性窗格中向下滚动时，您将获得其他数据，如 HANA 大型实例单元的唯一资源 ID 或分配给部署的订阅 ID。

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>通过 Azure 门户重新启动 HANA 大型实例单元
启动 Linux 操作系统的重新启动时，操作系统无法成功完成重新启动。 要强制重新启动，您需要打开服务请求，让 Microsoft 操作执行 HANA 大型实例单元的电源重新启动。 HANA 大型实例单元的电源重新启动功能现在集成到 Azure 门户中。 当您在 HANA 大型实例单元的概述部分中时，您会看到在数据部分顶部重新启动的按钮

![重新启动 Azure 门户中的步骤#1](./media/hana-li-portal/portal-restart-first-step.png)

按下重新启动按钮时，系统会询问您是否确实要重新启动设备。 当您通过按下按钮"是"进行确认时，设备将重新启动。

> [!NOTE]
> 在重新启动过程中，您将经历一小段时间，设备的状态更改为 **"开始**"移动到 **"已启动"** 状态。 处于 **"已启动"** 状态意味着操作系统正在启动或操作系统已完全启动。 因此，重新启动设备后，您不能指望在状态切换到 **"已启动"** 时立即登录到设备。

> [!IMPORTANT]
> 根据 HANA 大型实例单元中的内存量，重新启动和重新启动硬件和操作系统最多可能需要一个小时


## <a name="open-a-support-request-for-hana-large-instances"></a>打开 HANA 大型实例的支持请求
在 HANA 大型实例单元的 Azure 门户显示中，还可以为 HANA 大型实例单元专门创建支持请求。 当您关注链接**新支持请求时** 

![在 Azure 门户中启动服务请求步骤#1](./media/hana-li-portal/portal-initiate-support-request.png)

为了获得下一个屏幕中列出的 SAP HANA 大型实例的服务，您可能需要选择如下所示的"所有服务"

![选择 Azure 门户中的所有服务](./media/hana-li-portal/portal-create-service-request.png)

在服务列表中，可以找到服务**SAP HANA 大型实例**。 选择该服务时，可以选择特定问题类型，如下所示：


![在 Azure 门户中选择问题类](./media/hana-li-portal/portal-select-problem-class.png)

在每个不同的问题类型下，您都会选择一系列问题子类型，以便进一步描述问题的特征。 选择子类型后，您现在可以为主题命名。 完成选择过程后，可以转到创建的下一步。 在 **"解决方案"** 部分中，您可以指向有关 HANA 大型实例的文档，这可能提供指向问题解决方案的指针。 如果在建议的文档中找不到问题的解决方案，请转到下一步。 在下一步中，系统将询问问题是 VM 还是 HANA 大型实例单元。 此信息有助于将支持请求定向到正确的专家。 

![Azure 门户中支持案例的详细信息](./media/hana-li-portal/portal-support-request-details.png)

当您回答问题并提供其他详细信息时，您可以执行下一步以查看支持请求并提交支持请求。

## <a name="next-steps"></a>后续步骤

- [如何监视 Azure 上的 SAP HANA（大型实例）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [HANA 端的监视和故障排除](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)


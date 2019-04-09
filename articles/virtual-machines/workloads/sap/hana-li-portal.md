---
title: Azure HANA 大型实例控制通过 Azure 门户 |Microsoft Docs
description: 介绍如何确定并通过门户与 Azure HANA 大型实例进行交互
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/02/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8240308b3e0955b1d4d3ef2e82cad215daf95b00
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009362"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>通过 Azure 门户控制 Azure HANA 大型实例
本文档介绍如何[HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)中显示[Azure 门户](https://portal.azure.com)，可以通过 Azure 门户中使用为你部署的 HANA 大型实例单元执行的活动。 在 Azure 门户中的 HANA 大型实例的可见性是通过 Azure 资源提供程序提供适用于 HANA 大型实例，当前处于公共预览状态

## <a name="register-hana-large-instance-resource-provider"></a>注册 HANA 大型实例资源提供程序
通常为 HANA 大型实例资源提供程序注册 Azure 订阅所使用的 HANA 大型实例部署。 但是，如果您无法查看部署 HANA 大型实例单元，则应在 Azure 订阅中注册资源提供程序。 有两种方法注册了 HANA 大型实例资源提供程序

### <a name="register-through-cli-interface"></a>通过 CLI 接口注册
您需要登录到 Azure 订阅，用于 HANA 大型实例部署通过 Azure CLI 接口。 你可以重新注册 HANA 大型实例提供程序使用以下命令：
    
    az provider register --namespace Microsoft.HanaOnAzure

有关详细信息，请参阅文章[Azure 资源提供程序和类型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)


### <a name="register-through-azure-portal"></a>通过 Azure 门户注册
你可以重新注册 HANA 大型实例资源提供程序通过 Azure 门户。 需要列出你的订阅在 Azure 门户中，双击该订阅，用于部署 HANA 大型实例单元上。 一个位于你的订阅，概述页上选择"资源提供程序"，如下所示，搜索窗口中键入"HANA"。 

![通过 Azure 门户注册 HLI RP](./media/hana-li-portal/portal-register-hli-rp.png)

在屏幕截图中所示，已注册资源提供程序。 如果尚未注册资源提供程序，请按"重新注册"注册"。

有关详细信息，请参阅文章[Azure 资源提供程序和类型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>在 Azure 门户中的 HANA 大型实例单元的显示
提交 HANA 大型实例部署申请时，系统会要求你指定要连接到 HANA 大型实例的 Azure 订阅。 建议，使用您用来部署适用于 HANA 大型实例单元的 SAP 应用层的同一个订阅。
为你的第一个 HANA 大型实例进行部署，一个新[Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)在 HANA 大型实例的部署请求中提交的 Azure 订阅中创建。  新的资源组将列出已部署的特定订阅中的所有 HANA 大型实例单元。

若要查找新的 Azure 资源组，请列出资源组在订阅中通过 Azure 门户的左侧的导航窗格中导航

![在 Azure 门户中的导航窗格](./media/hana-li-portal/portal-resource-group.png)

在资源组列表中，你获取列出，你可能需要对用于已部署的 HANA 大型实例的订阅进行筛选

![在 Azure 门户中的筛选器资源组](./media/hana-li-portal/portal-filtering-subscription.png)

在筛选之后到正确的订阅，你仍可能列表很长的资源组。 查找另一个使用的后修补程序 **-Txxx** "xxx"的三位数字，如 **-T050**。 

当发现的资源组，列出它的详细信息。 你收到了列表可能如下所示：

![在 Azure 门户中的 HLI 列表](./media/hana-li-portal/portal-hli-units-list.png)

列出的所有单元都表示已在订阅中部署的单个 HANA 大型实例单元。 在这种情况下，您查看八个不同 HANA 大型实例单位，已部署在订阅中。

如果您部署了同一 Azure 订阅下的多个 HANA 大型实例租户，您会发现多个 Azure 资源组 


## <a name="look-at-attributes-of-single-hli-unit"></a>查看单个 HLI 单元属性
在 HANA 大型实例单元的列表中，可以单击单个单元，并转到单个 HANA 大型实例单元的详细信息。 

在概述屏幕中，如果您听到的单元，如下所示的演示文稿：

![显示 HLI 单元的概述](./media/hana-li-portal/portal-show-overview.png)

查看所示的不同属性，这些属性查找几乎不不同于 Azure VM 属性。 左侧标题中，它显示了资源组、 Azure 区域、 订阅名称和 ID 以及你添加一些标记。 默认情况下，在 HANA 大型实例单元有分配任何标记。 标头的右侧，部署已完成时所分配列出的单位的名称。 操作系统显示的 IP 地址及。 为 Vm 的 HANA 大型实例单元类型的 CPU 数量的线程和内存还会显示。 在不同的 HANA 大型实例单位，更多详细信息如下所示：

- [对 HLI 可用的 SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [SAP HANA（大型实例）存储体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

有关 HANA 大型实例单元的电源状态通知的标头的右列中的附加字段。

> [!NOTE]
> 电源状态描述硬件单元提供支持打开或关闭。 它使操作系统已启动并运行有关的信息。 随着您重新启动一个 HANA 大型实例单元，您将遇到的单元状态更改为较短时间**起始**若要将移动到的状态**已启动**。 处于的状态**已启动**意味着 OS 启动了或的操作系统已启动完全。 因此，重启后的单元，您无法预测一旦状态切换到立即登录到单元**已启动**。
> 


## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>检查单个 HANA 大型实例单元的活动 
除了提供 HANA 大型实例单元的概述，可以检查特定单元的活动。 活动日志看起来类似于：

![在 Azure 门户中的导航窗格](./media/hana-li-portal/portal-activity-list.png)

记录的主要活动之一为单位的重新启动。 列出的数据包括活动，活动触发，时间戳的订阅 ID 的状态的活动触发和触发活动的 Azure 用户。 

获取记录的另一个活动是 Azure 元数据中的单元的更改。 除了重新启动，请参阅的活动**编写 HANAInstances**。 此类型的活动本身，在 HANA 大型实例单元上执行任何更改，但记录对在 Azure 中的单元的元数据的更改。 中列出的情况下，我们添加和删除标记 （请参阅下一节）。

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>添加和删除到 HANA 大型实例单元的 Azure 标记
你拥有的另一种可能性是将添加[标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)到 HANA 大型实例单元。 获取分配标记的方法不操作与将标记分配给 Vm 相同。 为与标记存在于 Azure 元数据，以及适用于 HANA 大型实例的 Vm 具有与标记的 Vm 相同的限制。

删除的标记与 Vm 的工作方式相同。 将特定的 HANA 大型实例单元的活动日志中列出这两个活动，将应用和删除标记。

## <a name="check-properties-of-a-hana-large-instance-unit"></a>检查 HANA 大型实例单元的属性
部分**属性**包括时实例移交给您获得的重要信息。 它是从哪里获得可能需要在支持的所有信息的部分用例或将设置存储快照的配置时需要。 这种情况下此部分是围绕你的实例，连接到 Azure，并存储后端实例的数据的集合。 部分顶部的如下所示：


![在 Azure 门户中的 HLI 属性的上半部分](./media/hana-li-portal/portal-properties-top.png)

第一个的几个数据项，您了解了在概述屏幕中已。 但数据的重要部分是 ExpressRoute 线路 ID，如第一个部署的单位移交获得。 在支持某些情况下，可能会获取要求您为该数据。 在屏幕截图的底部显示重要数据条目。 显示的数据是隔离存储，NFS 存储头的 IP 地址你**租户**HANA 大型实例堆栈中。 在编辑时也需要此 IP 地址[存储的配置文件快照备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots)。 

在您向下滚动属性窗格中，你会获得其他数据，如 HANA 大型实例单元，唯一的资源 ID 或分配给部署的订阅 ID。

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>重新启动通过 Azure 门户的 HANA 大型实例单位
在开始重启的 Linux 操作系统，没有各种情况下，操作系统无法完成重新启动成功。 若要强制重新启动，则需要提出服务请求并执行 power 重启的 HANA 大型实例单元的 Microsoft 操作。 Power 重启的 HANA 大型实例单元的功能现已集成到 Azure 门户中。 由于在 HANA 大型实例单元的概述部分中，请参阅重新启动数据部分顶部的按钮

![在 Azure 门户中重新启动步骤 #1](./media/hana-li-portal/portal-restart-first-step.png)

因为按下的重新启动按钮，会要求是否确实要重新启动单元。 由于确认"是"按下按钮时，设备将重新启动。

> [!NOTE]
> 在重新启动过程中，您将遇到的单元状态更改为较短时间**起始**若要将移动到的状态**已启动**。 处于的状态**已启动**意味着 OS 启动了或的操作系统已启动完全。 因此，重启后的单元，您无法预测一旦状态切换到立即登录到单元**已启动**。

> [!IMPORTANT]
> 取决于 HANA 大型实例单元中的内存量，重新启动和重启的硬件和操作系统需要一小时


## <a name="open-a-support-request-for-hana-large-instances"></a>为 HANA 大型实例打开支持请求
从 Azure 门户显示中的 HANA 大型实例单元，可以专门为 HANA 大型实例单元也创建支持请求。 在按照链接**新建支持请求** 

![启动 Azure 门户中的服务请求步骤 #1](./media/hana-li-portal/portal-initiate-support-request.png)

若要获取下一个屏幕中列出的 SAP HANA 大型实例的服务，你可能需要选择所有服务"，如下所示

![在 Azure 门户中选择的所有服务](./media/hana-li-portal/portal-create-service-request.png)

在服务列表中，可以找到服务**SAP HANA 大型实例**。 选择该服务，可以选择特定的问题类型，如所示：


![在 Azure 门户中选择问题类](./media/hana-li-portal/portal-select-problem-class.png)

在每个不同的问题类型，提供一系列问题子类型，您需要选择以描述您的问题更多的特征。 选择相应的子类型之后, 您现在可以命名该主题。 一旦您完成选择过程后，您可以移动到下一步创建。 在中**解决方案**部分中，您有指向围绕 HANA 大型实例文档，其命名为一个指针，您的问题的解决方案。 如果你遇到的问题建议的文档中，找不到一种解决方案，则转到下一步。 在下一步，您将向你询问问题是否与 Vm 或 HANA 大型实例单元。 此信息可帮助将定向到正确的专家支持请求。 

![在 Azure 门户中的支持案例的详细信息](./media/hana-li-portal/portal-support-request-details.png)

当你的问题的回答，并提供更多详细信息，可以转下一步以查看支持请求和提交它。

## <a name="next-steps"></a>后续步骤

- [如何监视 Azure 上的 SAP HANA（大型实例）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [HANA 端的监视和故障排除](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)


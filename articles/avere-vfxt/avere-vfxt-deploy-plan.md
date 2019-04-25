---
title: 规划 Avere vFXT 系统 - Azure
description: 介绍部署 Avere vFXT for Azure 之前的规划工作
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 46978d19a0789bb43e861ca89661aa5b78eb4ec7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409868"
---
# <a name="plan-your-avere-vfxt-system"></a>规划 Avere vFXT 系统

本文介绍如何规划 Azure 定位并根据需要适当地设置大小的群集新 Avere vFXT。 

在访问 Azure 市场或创建任何 VM 之前，请考虑群集如何与 Azure 中的其他元素交互。 规划好要将群集资源定位在专用网络和子网中的哪个位置，并确定后端存储的位置。 确保创建的群集节点足够强大，可以支持自己的工作流。 

请继续阅读了解更多信息。

## <a name="resource-group-and-network-infrastructure"></a>资源组和网络基础结构

考虑好 Avere vFXT for Azure 部署元素的位置。 下图显示了 Avere vFXT for Azure 组件的可能排列方式：

![显示一个子网中的群集控制器和群集 VM 的示意图。 子网边界的周围是 VNet 边界。 VNet 中的六边形表示存储服务终结点；它连接到 VNet 外部的 Blob 存储，如虚线箭头所示。](media/avere-vfxt-components-option.png)

规划 Avere vFXT 系统的网络基础结构时，请遵循以下准则：

* 应使用为 Avere vFXT 部署创建的新订阅来管理所有元素。 优势包括： 
  * 更简单的成本跟踪 - 在一个订阅中查看和审核由资源、基础结构和计算循环产生的所有成本。
  * 更轻松的清理 - 完成项目后，可以删除整个订阅。
  * 方便分区资源的配额-保护其他关键工作负荷免受可能限制通过隔离 Avere vFXT 客户端和单个订阅中的群集的资源。 提供大量的高性能计算工作流的客户端时，这可以避免冲突。

* 将客户端计算系统定位在靠近 vFXT 群集的位置。 后端存储可以位于较远的位置。  

* VFXT 群集和群集控制器 VM 应位于同一资源组中，同一虚拟网络 (vnet) 中使用相同的存储帐户。 自动化的群集创建模板处理大多数情况下。

* 群集必须位于其自身的子网中，以避免与客户端或计算资源发生 IP 地址冲突。 

* 群集创建模板可以创建该群集，包括资源组、 虚拟网络、 子网，以及存储帐户的所需的基础结构资源的大多数。 如果你想要使用已存在的资源，请确保它们满足此表中的要求。 

  | 资源 | 使用现有？ | 要求 |
  |----------|-----------|----------|
  | 资源组 | 是的如果为空 | 必须为空| 
  | 存储帐户 | 如果创建群集后连接的现有 Blob 容器是 <br/>  如果在群集创建过程中创建新的 Blob 容器否 | 现有的 Blob 容器必须为空 <br/> &nbsp; |
  | 虚拟网络 | 是 | 如果创建新的 Azure Blob 容器必须包含存储服务终结点 | 
  | 子网 | 是 |   |

## <a name="ip-address-requirements"></a>IP 地址要求 

确保群集的子网具有足够大的 IP 地址范围，以支持群集。 

Avere vFXT 群集使用以下 IP 地址：

* 一个群集管理 IP 地址。 此地址可在群集中的不同节点之间移动，但必须始终可用，以便可以连接到 Avere 控制面板配置工具。
* 对于每个群集节点：
  * 至少有一个面向客户端的 IP 地址。 （所有面向客户端的地址由群集的 *vserver* 管理，此工具可根据需要在节点之间移动这些地址。）
  * 一个用于群集通信的 IP 地址
  * 一个实例 IP 地址（分配给 VM）

如果使用 Azure Blob 存储，则还可能需要群集 VNet 中的 IP 地址：  

* 一个 Azure Blob 存储帐户需要至少五个 IP 地址。 如果将 Blob 存储定位在群集所在的同一 VNet 中，请记住此要求。
* 如果使用群集虚拟网络外部的 Azure Blob 存储，应在 VNet 中创建存储服务终结点。 此终结点不使用 IP 地址。

可以选择将网络资源和 Blob 存储（如果已使用）定位在不包含该群集的其他资源组中。

## <a name="vfxt-node-size"></a>vFXT 节点大小

充当群集节点的 VM 决定了请求吞吐量和缓存的存储容量。 <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

每个 vFXT 节点是相同的。 也就是说，如果创建三节点群集，则会获得三个具有相同类型和大小的 VM。 

| 实例类型 | vCPU | 内存  | 本地 SSD 存储  | 最大数据磁盘数 | 非缓存磁盘吞吐量 | NIC（计数） |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51,200 IOPS <br/> 768 MBps | 16,000 MBps (8)  |

每个节点的磁盘缓存可配置，范围为 1000 GB 到 8000 GB。 每个节点的 4 TB 是 Standard_E32s_v3 节点的建议的缓存大小。

有关这些虚拟机的其他信息，请参阅 Microsoft Azure 文档：

* [内存优化虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>帐户配额

确保订阅具有足够的容量，可以运行 Avere vFXT 群集以及所用的任何计算系统或客户端系统。 有关详细信息，请阅读 [vFXT 群集的配额](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)。

## <a name="back-end-data-storage"></a>后端数据存储

当数据不在缓存中时，Avere vFXT 群集应当将数据存储在何处？ 决定你的工作集是要长期存储在新的 Blob 容器中、现有云中还是硬件存储系统中。 

若要对后端使用 Azure Blob 存储，应在创建 vFXT 群集的过程中创建一个新容器。 此选项会创建并配置新容器，以便在准备好群集后，该容器可供使用。 

有关详细信息，请阅读[创建 Avere vFXT for Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure)。

> [!NOTE]
> 只能将空的 Blob 存储容器用作 Avere vFXT 系统的核心文件管理器。 vFXT 必须能够管理其对象存储，而无需保留现有数据。 
>
> 请阅读[将数据移到 vFXT 群集](avere-vfxt-data-ingest.md)，了解如何使用客户端计算机和 Avere vFXT 缓存高效地将数据复制到群集的新容器。

若要使用现有的本地存储系统，必须在创建 vFXT 群集后将该系统添加到其中。 有关如何将现有存储系统添加到 Avere vFXT 群集的详细说明，请阅读[配置存储](avere-vfxt-add-storage.md)。

## <a name="cluster-access"></a>群集访问 

Avere vFXT for Azure 群集位于专用子网中，并且该群集没有公用 IP 地址。 必须采用某个方法来访问专用子网以执行群集管理和客户端连接。 

访问选项包括：

* 跳转主机 - 向专用网络中的一个单独 VM 分配一个公用 IP 地址，并使用它来创建到群集节点的 SSL 隧道。 

  > [!TIP]
  > 如果在群集控制器上设置公用 IP 地址，则可以使用它作为跳转主机。 有关详细信息，请阅读[群集控制器用作跳转主机](#cluster-controller-as-jump-host)。

* 虚拟专用网络 (VPN) - 配置到专用网络的点到站点或站点到站点 VPN。

* Azure ExpressRoute - 通过任一 ExpressRoute 合作伙伴配置专用连接。 

有关这些选项的详细信息，请参阅[有关 Internet 通信的 Azure 虚拟网络文档](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)。

### <a name="cluster-controller-as-jump-host"></a>群集控制器用作跳转主机

如果你在群集控制器上设置了公用 IP 地址，则可以使用它作为跳转主机来从专用子网外部联系 Avere vFXT 群集。 但是，因为控制器有权修改群集节点，这会带来小小的安全风险。  

为了提高安全性具有公共 IP 地址的控制器，部署脚本会自动创建限制为仅端口 22 的入站的访问的网络安全组。 可以通过锁定对 IP 源地址范围的访问来进一步保护系统，换句话说，只允许从你打算将其用于群集访问的计算机进行连接。

创建群集时，可以选择是否在群集控制器上创建公用 IP 地址。 

* 如果创建新的 VNET 或新子网，系统会为群集控制器分配一个公用 IP 地址。
* 如果选择现有的 VNET 和子网，则群集控制器将仅具有专用 IP 地址。 

## <a name="vm-access-roles"></a>VM 访问角色 

Azure 使用[基于角色的访问控制](../role-based-access-control/index.yml)(RBAC) 授权群集 Vm 执行某些任务。 例如，群集控制器需要授权才能创建和配置群集节点 Vm。 群集节点需要能够分配或重新分配到其他群集节点的 IP 地址。

两个内置的 Azure 角色用于 Avere vFXT 虚拟机： 

* 群集控制器使用的内置角色[Avere 参与者](../role-based-access-control/built-in-roles.md#avere-contributor)。 
* 群集节点使用的内置角色[Avere 运算符](../role-based-access-control/built-in-roles.md#avere-operator)

如果需要自定义 Avere vFXT 组件的访问权限角色，必须定义你自己的角色并向 Vm 中在创建时分配它。 不能在 Azure Marketplace 中使用部署模板。 查阅 Microsoft 客户服务和支持通过 Azure 门户中打开票证，如中所述[获取有关您的系统的帮助](avere-vfxt-open-ticket.md)。 

## <a name="next-step-understand-the-deployment-process"></a>后续步骤：了解部署过程

[部署概述](avere-vfxt-deploy-overview.md)大致介绍了创建 Avere vFXT for Azure 系统并使其准备好提供数据所要执行的所有步骤。  
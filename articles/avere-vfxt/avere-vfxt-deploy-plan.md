---
title: 规划 Avere vFXT 系统 - Azure
description: 介绍部署 Avere vFXT for Azure 之前的规划工作
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: cd0c74c8aa40b3e96716ef37aa27b08b5f6aece1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547534"
---
# <a name="plan-your-avere-vfxt-system"></a>规划 Avere vFXT 系统

本文介绍如何为 Azure 群集规划新的 Avere vFXT，该群集根据您的需要定位和调整大小。

在去 Azure 应用商店或创建任何 VM 之前，请考虑以下详细信息：

* 群集将如何与其他 Azure 资源交互？
* 群集元素应位于专用网络和子网中的位置？
* 您将使用哪种类型的后端存储，群集将如何访问它？
* 群集节点需要多大的强大功能来支持您的工作流？

请继续阅读了解更多信息。

## <a name="learn-the-components-of-the-system"></a>了解系统的组件

在开始规划时，了解 Azure 系统的 Avere vFXT 的组件会很有帮助。

* 群集节点 - 群集由配置为群集节点的三个或多个 VM 组成。 节点越多，系统吞吐量就越高，缓存也更大。

* 缓存 - 缓存容量在群集节点之间平均分配。 创建群集时设置每个节点的缓存大小;节点大小将添加为总缓存大小。

* 群集控制器 - 群集控制器是位于与群集节点相同的子网内的其他 VM。 创建群集和持续管理任务需要控制器。

* 后端存储 - 要缓存的数据长期存储在硬件存储系统或 Azure Blob 容器中。 可以为 Azure 群集创建 Avere vFXT 后添加存储，或者通过使用 Blob 存储可以在创建群集时添加和配置容器。

* 客户端 - 使用缓存文件的客户端计算机使用虚拟文件路径连接到群集，而不是直接访问存储系统。 （在["Avere vFXT"群集中](avere-vfxt-mount-clients.md)阅读更多内容。

## <a name="subscription-resource-group-and-network-infrastructure"></a>订阅、资源组和网络基础结构

考虑好 Avere vFXT for Azure 部署元素的位置。 下图显示了 Avere vFXT for Azure 组件的可能排列方式：

![显示一个子网中的群集控制器和群集 VM 的示意图。 子网边界的周围是 VNet 边界。 VNet 中的六边形表示存储服务终结点；它连接到 VNet 外部的 Blob 存储，如虚线箭头所示。](media/avere-vfxt-components-option.png)

规划 Avere vFXT 群集的网络基础结构时，请遵循以下准则：

* 为每个 Avere vFXT 创建用于 Azure 部署的新订阅。 管理此订阅中的所有组件。

  为每个部署使用新订阅的好处包括：
  * 更简单的成本跟踪 - 在一个订阅中查看和审核由资源、基础结构和计算循环产生的所有成本。
  * 更轻松的清理 - 完成项目后，可以删除整个订阅。
  * 方便划分资源配额 - 将 Avere vFXT 客户端和群集隔离到单个订阅中，以保护其他关键工作负载免受可能的资源限制。 这种分离可防止在为高性能计算工作流中引入大量客户端时发生冲突。

* 将客户端计算系统定位在靠近 vFXT 群集的位置。 后端存储可以位于较远的位置。  

* 一起查找 vFXT 群集和群集控制器 VM - 具体来说，它们应该是：

  * 在同一虚拟网络中
  * 在同一资源组中
  * 使用相同的存储帐户
  
  在大多数情况下，群集创建模板处理此配置。

* 群集必须位于其自己的子网中，以避免与客户端或其他计算资源发生 IP 地址冲突。

* 使用群集创建模板为群集创建所需的大多数基础结构资源，包括资源组、虚拟网络、子网和存储帐户。

  如果要使用已经存在的资源，请确保它们满足此表中的要求。

  | 资源 | 使用现有？ | 要求 |
  |----------|-----------|----------|
  | 资源组 | 是，如果为空 | 必须为空|
  | 存储帐户 | 如果群集创建后连接现有 Blob 容器，**则为** <br/>  如果在群集创建期间创建新 Blob 容器，**则否** | 现有 Blob 容器必须为空 <br/> &nbsp; |
  | 虚拟网络 | 是 | 如果创建新的 Azure Blob 容器，则必须包括存储服务终结点 |
  | 子网 | 是 | 无法包含其他资源 |

## <a name="ip-address-requirements"></a>IP 地址要求

确保群集的子网具有足够大的 IP 地址范围，以支持群集。

Avere vFXT 群集使用以下 IP 地址：

* 一个群集管理 IP 地址。 此地址可以根据需要从群集中的节点移动到一个节点，以便始终可用。 使用此地址可连接到 Avere 控制面板配置工具。
* 对于每个群集节点：
  * 至少有一个面向客户端的 IP 地址。 （所有面向客户端的地址都由群集的*vserver*管理，它可以根据需要在节点之间移动 IP 地址。
  * 一个用于群集通信的 IP 地址
  * 一个实例 IP 地址（分配给 VM）

如果使用 Azure Blob 存储，它可能还需要群集虚拟网络中的 IP 地址：  

* 一个 Azure Blob 存储帐户需要至少五个 IP 地址。 如果找到与群集相同的虚拟网络中的 Blob 存储，请记住此要求。
* 如果使用群集虚拟网络外的 Azure Blob 存储，请在虚拟网络内创建存储服务终结点。 终结点不使用 IP 地址。

可以选择将网络资源和 Blob 存储（如果已使用）定位在不包含该群集的其他资源组中。

## <a name="vfxt-node-size"></a>vFXT 节点大小

充当群集节点的 VM 决定了请求吞吐量和缓存的存储容量。 <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

每个 vFXT 节点是相同的。 也就是说，如果创建三节点群集，则会获得三个具有相同类型和大小的 VM。

| 实例类型 | vCPU | 内存  | 本地 SSD 存储  | 最大数据磁盘数 | 非缓存磁盘吞吐量 | NIC（计数） |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51,200 IOPS <br/> 768 MBps | 16,000 MBps (8)  |

每个节点的磁盘缓存可配置，范围为 1000 GB 到 8000 GB。 每个节点 4 TB 是 Standard_E32s_v3节点的建议缓存大小。

有关这些 VM 的其他信息，请阅读 Microsoft Azure 文档：[内存优化的虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>帐户配额

确保订阅具有足够的容量，可以运行 Avere vFXT 群集以及所用的任何计算系统或客户端系统。 有关详细信息，请阅读 [vFXT 群集的配额](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)。

## <a name="back-end-data-storage"></a>后端数据存储

后端存储系统既向群集的缓存提供文件，也从缓存接收更改的数据。 决定您的工作集是长期存储在新的 Blob 容器中还是存储在现有存储系统（云或硬件）中。 这些后端存储系统称为*核心文件服务器*。

### <a name="hardware-core-filers"></a>硬件核心文件服务器

创建群集后，将硬件存储系统添加到 vFXT 群集。 只要可以从群集的子网到达存储系统，就可以使用各种流行的硬件系统，包括本地系统。

有关如何将现有存储系统添加到 Avere vFXT 群集的详细说明，请阅读[配置存储](avere-vfxt-add-storage.md)。

### <a name="cloud-core-filers"></a>云核心文件服务器

Azure 系统的 Avere vFXT 可以使用空 Blob 容器进行后端存储。 容器在添加到群集时必须为空 - vFXT 系统必须能够管理其对象存储，而无需保留现有数据。

> [!TIP]
> 如果要对后端使用 Azure Blob 存储，请创建一个新容器作为创建 vFXT 群集的一部分。 群集创建模板可以创建和配置新的 Blob 容器，以便在群集可用后立即可以使用该容器。 以后添加容器会更加复杂。
>
> 有关详细信息，请阅读[创建 Avere vFXT for Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure)。

将空 Blob 存储容器添加为核心文件程序后，可以通过群集将数据复制到该容器。 使用并行的多线程复制机制。 请阅读[将数据移到 vFXT 群集](avere-vfxt-data-ingest.md)，了解如何使用客户端计算机和 Avere vFXT 缓存高效地将数据复制到群集的新容器。

## <a name="cluster-access"></a>群集访问

Avere vFXT for Azure 群集位于专用子网中，并且该群集没有公共 IP 地址。 您必须有某种方法来访问群集管理和客户端连接的专用子网。

访问选项包括：

* 跳转主机 - 向专用网络中的一个单独 VM 分配一个公共 IP 地址，并使用它来创建到群集节点的 SSL 隧道。

  > [!TIP]
  > 如果在群集控制器上设置公共 IP 地址，则可以使用它作为跳转主机。 有关详细信息，请阅读[群集控制器用作跳转主机](#cluster-controller-as-jump-host)。

* 虚拟专用网络 （VPN） - 在 Azure 中的专用网络和企业网络之间配置点对点或站点到站点 VPN。

* Azure ExpressRoute - 通过任一 ExpressRoute 合作伙伴配置专用连接。

有关这些选项的详细信息，请参阅[有关 Internet 通信的 Azure 虚拟网络文档](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)。

### <a name="cluster-controller-as-jump-host"></a>群集控制器用作跳转主机

如果你在群集控制器上设置了公共 IP 地址，则可以使用它作为跳转主机来从专用子网外部联系 Avere vFXT 群集。 但是，因为控制器有权修改群集节点，这会带来小小的安全风险。

为了提高具有公共 IP 地址的控制器的安全性，部署脚本会自动创建一个网络安全组，该组仅限制对端口 22 的入站访问。 可以通过锁定对 IP 源地址范围的访问来进一步保护系统，换句话说，只允许从你打算将其用于群集访问的计算机进行连接。

创建群集时，可以选择是否在群集控制器上创建公共 IP 地址。

* 如果创建新**的虚拟网络**或**新子网**，将为群集控制器分配**公共**IP 地址。
* 如果选择现有的虚拟网络和子网，群集控制器将仅具有**专用**IP 地址。

## <a name="vm-access-roles"></a>VM 访问角色

Azure 使用[基于角色的访问控制](../role-based-access-control/index.yml)（RBAC） 授权群集 VM 执行某些任务。 例如，群集控制器需要授权才能创建和配置群集节点 VM。 群集节点需要能够将 IP 地址分配给其他群集节点或重新分配 IP 地址。

Avere vFXT 虚拟机使用两个内置 Azure 角色：

* 群集控制器使用内置角色[Avere 参与者](../role-based-access-control/built-in-roles.md#avere-contributor)。
* 群集节点使用内置角色[Avere 运算符](../role-based-access-control/built-in-roles.md#avere-operator)。

如果需要为 Avere vFXT 组件自定义访问角色，则必须定义自己的角色，然后在创建这些角色时将其分配给 VM。 不能在 Azure 应用商店中使用部署模板。 通过在 Azure 门户中打开票证，请参阅 Microsoft 客户服务和支持，如[获取系统帮助](avere-vfxt-open-ticket.md)中所述。

## <a name="next-steps"></a>后续步骤

[部署概述](avere-vfxt-deploy-overview.md)提供了为 Azure 系统创建 Avere vFXT 并使其准备好提供数据所需的步骤的全局视图。

---
title: 规划 Avere vFXT 系统 - Azure
description: 介绍部署 Avere vFXT for Azure 之前的规划工作
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: rohogue
ms.openlocfilehash: d4fc2a6b7def4b7c55faa37fbed756fbb830ff73
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75415438"
---
# <a name="plan-your-avere-vfxt-system"></a>规划 Avere vFXT 系统

本文介绍了如何为 Azure 群集规划新的 Avere vFXT，并根据需要调整其大小。

在访问 Azure 市场或创建任何 VM 之前，请考虑群集如何与 Azure 中的其他元素交互。 规划好要将群集资源定位在专用网络和子网中的哪个位置，并确定后端存储的位置。 确保创建的群集节点足够强大，可以支持自己的工作流。

继续阅读，了解详细信息。

## <a name="learn-the-components-of-the-system"></a>了解系统的组件

当你开始规划时，了解 Azure 系统的 Avere vFXT 组件会很有帮助。

* 群集节点-群集由三个或更多个 Vm 配置为群集节点。 更多节点会给予系统更高的吞吐量和更大的缓存。

* 缓存-缓存容量在群集节点之间平均划分。 创建群集时，设置每节点缓存大小;添加节点大小以成为总缓存大小。

* 群集控制器-群集控制器是位于与群集节点相同的子网内的其他 VM。 需要控制器来创建群集和进行日常管理任务。

* 后端存储-要缓存的数据存储在硬件存储系统或 Azure Blob 容器中的长期。 你可以在创建 Azure 群集的 Avere vFXT 之后添加存储，或者，如果使用 Blob 存储，则可以在创建群集时添加和配置容器。

* 客户端-使用缓存文件的客户端计算机使用虚拟文件路径连接到群集，而不是直接访问存储系统。 （有关详细信息，请参阅[装入 Avere vFXT 群集](avere-vfxt-mount-clients.md)。）

## <a name="subscription-resource-group-and-network-infrastructure"></a>订阅、资源组和网络基础结构

考虑好 Avere vFXT for Azure 部署元素的位置。 下图显示了 Avere vFXT for Azure 组件的可能排列方式：

![显示一个子网中的群集控制器和群集 VM 的示意图。 子网边界的周围是 VNet 边界。 VNet 中的六边形表示存储服务终结点；它连接到 VNet 外部的 Blob 存储，如虚线箭头所示。](media/avere-vfxt-components-option.png)

规划 Avere vFXT 系统的网络基础结构时，请遵循以下准则：

* 为 Azure 部署的每个 Avere vFXT 创建新订阅，并管理此订阅中的所有组件。 优势包括：
  * 更简单的成本跟踪 - 在一个订阅中查看和审核由资源、基础结构和计算循环产生的所有成本。
  * 更轻松的清理 - 完成项目后，可以删除整个订阅。
  * 方便地分区资源配额-通过将 Avere vFXT 客户端和群集隔离在单个订阅中，可保护其他关键工作负荷免受可能的资源限制。 这可以避免在为高性能计算工作流带来大量客户端时出现冲突。

* 将客户端计算系统定位在靠近 vFXT 群集的位置。 后端存储可以位于较远的位置。  

* 将 vFXT 群集和群集控制器 VM 一起定位-具体而言，它们应该是：

  * 在同一虚拟网络中
  * 在同一资源组中
  * 在同一存储帐户中
  
  在大多数情况下，自动群集创建模板会处理这种情况。

* 群集必须位于其自己的子网中，以避免与客户端或其他计算资源发生 IP 地址冲突。

* 使用群集创建模板为群集创建大多数所需的基础结构资源，包括资源组、虚拟网络、子网和存储帐户。

  如果要使用已存在的资源，请确保它们满足此表中的要求。

  | 资源 | 使用现有的？ | 要求 |
  |----------|-----------|----------|
  | 资源组 | 是，如果为空 | 必须为空|
  | 存储帐户 | 如果在创建群集后连接现有 Blob 容器，则为 **"是"** <br/>  如果在群集创建过程中创建新的 Blob 容器，则为**否** | 现有 Blob 容器必须为空 <br/> &nbsp; |
  | 虚拟网络 | 是 | 如果创建新的 Azure Blob 容器，则必须包含存储服务终结点 |
  | 子网 | 是 |   |

## <a name="ip-address-requirements"></a>IP 地址要求

确保群集的子网具有足够大的 IP 地址范围，以支持群集。

Avere vFXT 群集使用以下 IP 地址：

* 一个群集管理 IP 地址。 此地址可以根据需要在群集中的节点之间移动，以使其始终可用。 使用此地址连接到 Avere 控制面板配置工具。
* 对于每个群集节点：
  * 至少有一个面向客户端的 IP 地址。 （所有面向客户端的地址由群集的 *vserver* 管理，此工具可根据需要在节点之间移动这些地址。）
  * 一个用于群集通信的 IP 地址
  * 一个实例 IP 地址（分配给 VM）

如果使用 Azure Blob 存储，它还可能需要群集的虚拟网络中的 IP 地址：  

* 一个 Azure Blob 存储帐户需要至少五个 IP 地址。 如果在与群集相同的虚拟网络中找到 Blob 存储，请记住这一要求。
* 如果使用群集的虚拟网络外部的 Azure Blob 存储，请在虚拟网络中创建存储服务终结点。 终结点不使用 IP 地址。

可以选择将网络资源和 Blob 存储（如果已使用）定位在不包含该群集的其他资源组中。

## <a name="vfxt-node-size"></a>vFXT 节点大小

充当群集节点的 VM 决定了请求吞吐量和缓存的存储容量。 <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

每个 vFXT 节点是相同的。 也就是说，如果创建三节点群集，则会获得三个具有相同类型和大小的 VM。

| 实例类型 | vCPU | 内存  | 本地 SSD 存储  | 最大数据磁盘数 | 非缓存磁盘吞吐量 | NIC（计数） |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51,200 IOPS <br/> 768 MBps | 16,000 MBps (8)  |

每个节点的磁盘缓存可配置，范围为 1000 GB 到 8000 GB。 每个节点 4 TB 是 Standard_E32s_v3 节点的建议缓存大小。

有关这些 Vm 的其他信息，请阅读 Microsoft Azure 文档：

* [内存优化虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>帐户配额

确保订阅具有足够的容量，可以运行 Avere vFXT 群集以及所用的任何计算系统或客户端系统。 有关详细信息，请阅读 [vFXT 群集的配额](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)。

## <a name="back-end-data-storage"></a>后端数据存储

后端存储系统会将文件提供给群集的缓存，还会接收来自缓存的已更改数据。 决定你的工作集是要长期存储在新的 Blob 容器中、现有云中还是硬件存储系统中。 这些后端存储系统称为*核心文件*系统。

### <a name="hardware-core-filers"></a>硬件核心文件

创建群集后，将硬件存储系统添加到 vFXT 群集。 你可以使用任何现有的本地硬件系统（包括本地系统），只要存储系统可以从群集的子网访问。

有关如何将现有存储系统添加到 Avere vFXT 群集的详细说明，请阅读[配置存储](avere-vfxt-add-storage.md)。

### <a name="cloud-core-filers"></a>Cloud core 文件

Azure 系统的 Avere vFXT 可以将空 Blob 容器用于后端存储。 添加到群集时，容器必须为空-vFXT 系统必须能够管理其对象存储，而无需保留现有数据。

> [!TIP]
> 如果要将 Azure Blob 存储用于后端，请在创建 vFXT 群集的过程中创建一个新容器。 群集创建模板可以创建和配置新的 Blob 容器，以便在群集可用时可以立即使用它。 稍后添加容器更复杂。
>
> 有关详细信息，请阅读[创建 Avere vFXT for Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure)。

添加空 Blob 存储容器作为核心文件服务器后，可以通过群集将数据复制到其中。 使用并行的多线程复制机制。 请阅读[将数据移到 vFXT 群集](avere-vfxt-data-ingest.md)，了解如何使用客户端计算机和 Avere vFXT 缓存高效地将数据复制到群集的新容器。

## <a name="cluster-access"></a>群集访问

Avere vFXT for Azure 群集位于专用子网中，并且该群集没有公共 IP 地址。 对于群集管理和客户端连接，你必须有某种方式访问专用子网。

访问选项包括：

* 跳转主机 - 向专用网络中的一个单独 VM 分配一个公共 IP 地址，并使用它来创建到群集节点的 SSL 隧道。

  > [!TIP]
  > 如果在群集控制器上设置公共 IP 地址，则可以使用它作为跳转主机。 有关详细信息，请阅读[群集控制器用作跳转主机](#cluster-controller-as-jump-host)。

* 虚拟专用网络 (VPN) - 配置到专用网络的点到站点或站点到站点 VPN。

* Azure ExpressRoute - 通过任一 ExpressRoute 合作伙伴配置专用连接。

有关这些选项的详细信息，请参阅[有关 Internet 通信的 Azure 虚拟网络文档](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)。

### <a name="cluster-controller-as-jump-host"></a>群集控制器用作跳转主机

如果你在群集控制器上设置了公共 IP 地址，则可以使用它作为跳转主机来从专用子网外部联系 Avere vFXT 群集。 但是，因为控制器有权修改群集节点，这会带来小小的安全风险。

为了提高具有公共 IP 地址的控制器的安全性，部署脚本会自动创建一个网络安全组，该安全组限制对端口22的入站访问。 可以通过锁定对 IP 源地址范围的访问来进一步保护系统，换句话说，只允许从你打算将其用于群集访问的计算机进行连接。

创建群集时，可以选择是否在群集控制器上创建公共 IP 地址。

* 如果创建新的**虚拟网络**或新的**子网**，则会为群集控制器分配**公共 IP 地址**。
* 如果选择现有虚拟网络和子网，则群集控制器将只有**专用**IP 地址。

## <a name="vm-access-roles"></a>VM 访问角色

Azure 使用[基于角色的访问控制](../role-based-access-control/index.yml)（RBAC）来授权群集 vm 执行特定任务。 例如，群集控制器需要授权才能创建和配置群集节点 Vm。 群集节点需要能够向其他群集节点分配或重新分配 IP 地址。

Azure 虚拟机的 Avere vFXT 使用了两个内置的 Azure 角色：

* 群集控制器使用内置角色[Avere 参与者](../role-based-access-control/built-in-roles.md#avere-contributor)。
* 群集节点使用内置角色[Avere 运算符](../role-based-access-control/built-in-roles.md#avere-operator)

如果需要自定义 Avere vFXT 组件的访问角色，则必须定义自己的角色，然后在创建 Vm 时将其分配给 Vm。 不能使用 Azure Marketplace 中的部署模板。 如通过[系统获取帮助](avere-vfxt-open-ticket.md)中所述，在 Azure 门户中打开票证，以咨询 Microsoft 客户服务和支持部门。

## <a name="next-step-understand-the-deployment-process"></a>后续步骤：了解部署过程

[部署概述](avere-vfxt-deploy-overview.md)提供了为 Azure 系统创建 Avere vFXT 所需步骤的大图视图，并使其准备好提供数据。

---
title: 规划 Avere vFXT 系统 - Azure
description: 介绍部署 Avere vFXT for Azure 之前的规划工作
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f0e5523565dc561ed457dbc340835ad1889cb876
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669864"
---
# <a name="plan-your-avere-vfxt-system"></a>规划 Avere vFXT 系统

本文介绍如何规划新的 Avere vFXT for Azure 群集，以确保根据需求正确定位和调整所要创建的群集。 

在访问 Azure 市场或创建任何 VM 之前，请考虑群集如何与 Azure 中的其他元素交互。 规划好要将群集资源定位在专用网络和子网中的哪个位置，并确定后端存储的位置。 确保创建的群集节点足够强大，可以支持自己的工作流。 

请继续阅读了解更多信息。

## <a name="resource-group-and-network-infrastructure"></a>资源组和网络基础结构

考虑好 Avere vFXT for Azure 部署元素的位置。 下图显示了 Avere vFXT for Azure 组件的可能排列方式：

![显示一个子网中的群集控制器和群集 VM 的示意图。 子网边界的周围是 VNet 边界。 VNet 中的六边形表示存储服务终结点；它连接到 VNet 外部的 Blob 存储，如虚线箭头所示。](media/avere-vfxt-components-option.png)

规划 Avere vFXT 系统的网络基础结构时，请遵循以下准则：

* 应使用为 Avere vFXT 部署创建的新订阅来管理所有元素。 此策略可以简化成本跟踪和执行清理，同时还有助于划分资源配额。 由于 Avere vFXT 用于大量的客户端，在单个订阅中隔离客户端和群集可以防止在客户端预配期间其他关键工作负荷的资源受到限制。

* 将客户端计算系统定位在靠近 vFXT 群集的位置。 后端存储可以位于较远的位置。  

* 为方便起见，请将 vFXT 群集和群集控制器 VM 定位在同一虚拟网络 (VNet) 和同一资源组中。 它们还应该使用相同的存储帐户。 

* 群集必须位于其自身的子网中，以避免与客户端或计算资源发生 IP 地址冲突。 

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

## <a name="vfxt-node-sizes"></a>vFXT 节点大小 

充当群集节点的 VM 决定了请求吞吐量和缓存的存储容量。 可以从具有不同内存、处理器和本地存储特征的两个实例类型中进行选择。 

每个 vFXT 节点是相同的。 也就是说，如果创建三节点群集，则会获得三个具有相同类型和大小的 VM。 

| 实例类型 | vCPU | 内存  | 本地 SSD 存储  | 最大数据磁盘数 | 非缓存磁盘吞吐量 | NIC（计数） |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D16s_v3 | 16  | 64 GiB  | 128 GiB  | 32 | 25,600 IOPS <br/> 384 MBps | 8,000 MBps (8) |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51,200 IOPS <br/> 768 MBps | 16,000 MBps (8)  |

每个节点的磁盘缓存可配置，范围为 1000 GB 到 8000 GB。 对于 Standard_D16s_v3 节点，建议的缓存大小为每个节点 1 TB；对于 Standard_E32s_v3 节点，则建议每个节点 4 TB。

有关这些 VM 的其他信息，请阅读以下 Microsoft Azure 文档：

* [常规用途虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general)
* [内存优化虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>帐户配额

确保订阅具有足够的容量，可以运行 Avere vFXT 群集以及所用的任何计算系统或客户端系统。 有关详细信息，请阅读 [vFXT 群集的配额](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)。

## <a name="back-end-data-storage"></a>后端数据存储

当工作集不在缓存中时，是要将它存储在新的 Blob 容器、现有云还是硬件存储系统中？

若要对后端使用 Azure Blob 存储，应在创建 vFXT 群集的过程中创建一个新容器。 使用 ``create-cloud-backed-container`` 部署脚本并提供新 Blob 容器的存储帐户。 此选项会创建并配置新容器，以便在准备好群集后，该容器可供使用。 有关详细信息，请阅读[创建节点和配置群集](avere-vfxt-deploy.md#create-nodes-and-configure-the-cluster)。

> [!NOTE]
> 只能将空的 Blob 存储容器用作 Avere vFXT 系统的核心文件管理器。 vFXT 必须能够管理其对象存储，而无需保留现有数据。 
>
> 请阅读[将数据移到 vFXT 群集](avere-vfxt-data-ingest.md)，了解如何使用客户端计算机和 Avere vFXT 缓存高效地将数据复制到群集的新容器。

若要使用现有的本地存储系统，必须在创建 vFXT 群集后将该系统添加到其中。 ``create-minimal-cluster`` 部署脚本会创建不带后端存储的 vFXT 群集。 有关如何将现有存储系统添加到 Avere vFXT 群集的详细说明，请阅读[配置存储](avere-vfxt-add-storage.md)。 

## <a name="next-step-understand-the-deployment-process"></a>后续步骤：了解部署过程

[部署概述](avere-vfxt-deploy-overview.md)大致介绍了创建 Avere vFXT for Azure 系统并使其准备好提供数据所要执行的所有步骤。  
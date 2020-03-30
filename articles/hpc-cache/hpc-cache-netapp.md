---
title: 使用 Azure HPC 缓存和 Azure NetApp 文件
description: 如何使用 Azure HPC 缓存来改进对 Azure NetApp 文件存储的数据的访问
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 38f9d0338ce4c47024d670e6d3ee89a97faecc91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238681"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>将 Azure HPC 缓存与 Azure NetApp 文件一起使用

您可以将[Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)用作 Azure HPC 缓存的存储目标。 本文介绍了两个服务如何协同工作，并给出了设置它们的技巧。

Azure NetApp 文件将其 ONTAP 操作系统与 Microsoft Azure 的可扩展性和速度相结合。 这种组合允许用户在不重写代码的情况下将已建立的工作流转移到云中。

添加 Azure HPC 缓存组件可以通过在一个聚合命名空间中显示多个 Azure NetApp 文件卷来提高文件访问。 它可以为位于其他服务区域中的卷提供边缘缓存。 它还可以按需提高在较低级别服务级别创建的卷的性能，以节省成本。

## <a name="overview"></a>概述

要使用 Azure NetApp 文件系统作为 Azure HPC 缓存的后端存储，请按照此过程操作。

1. 根据[下面的"计划系统"](#plan-your-azure-netapp-files-system)中的指南创建 Azure NetApp 文件系统和卷。
1. 在需要文件访问的区域创建 Azure HPC 缓存。 （使用创建 Azure [HPC 缓存](hpc-cache-create.md)中的说明。
1. 在缓存中定义指向 Azure NetApp 文件卷的[存储目标](#create-storage-targets-in-the-cache)。 为每个用于访问卷的唯一 IP 地址创建一个缓存存储目标。
1. 让客户端[装载 Azure HPC 缓存](#mount-storage-targets)，而不是直接安装 Azure NetApp 文件卷。

## <a name="plan-your-azure-netapp-files-system"></a>规划 Azure NetApp 文件系统

规划 Azure NetApp 文件系统时，请注意本节中的项目，以确保可以将其与 Azure HPC 缓存顺利集成。

在创建卷以与 Azure HPC 缓存一起使用之前，还要阅读[Azure NetApp 文件文档](../azure-netapp-files/index.yml)。

### <a name="nfs-client-access-only"></a>仅 NFS 客户端访问

Azure HPC 缓存当前仅支持 NFS 访问。 不能将其与 SMB ACL 或 POSIX 模式位卷一起使用。

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Azure NetApp 文件的独占子网

Azure NetApp 文件对其卷使用单个委派子网。 任何其他资源都无法使用该子网。 此外，虚拟网络中只有一个子网可用于 Azure NetApp 文件。 有关详细信息，在[Azure NetApp 文件网络规划指南](../azure-netapp-files/azure-netapp-files-network-topologies.md)中。

### <a name="delegated-subnet-size"></a>委派子网大小

创建要与 Azure HPC 缓存一起使用的 Azure NetApp 文件系统时，请使用委派子网的最小大小。

使用网掩码 /28 指定的最小大小提供 16 个 IP 地址。 实际上，Azure NetApp 文件仅使用其中三个可用的 IP 地址进行卷访问。 这意味着您只需在 Azure HPC 缓存中创建三个存储目标，即覆盖所有卷。

如果委派的子网太大，Azure NetApp 文件卷使用的 IP 地址可能比单个 Azure HPC 缓存实例可以处理的 IP 地址多。 单个缓存最多只能有 10 个存储目标。

Azure NetApp 文件文档中的快速入门示例对委派的子网使用 10.7.0.0/16，该子网提供过大的子网。

### <a name="capacity-pool-service-level"></a>容量池服务级别

为容量池选择服务级别时，请考虑工作流。 如果经常将数据写回 Azure NetApp 文件卷，则如果回写时间较慢，缓存的性能可能会受到限制。 为频繁写入的卷选择高服务级别。

服务级别较低的卷在缓存预填充内容时，在任务开始时也可能显示一些延迟。 在缓存启动并运行完一组良好的工作文件后，延迟应变得不明显。

提前规划容量池服务级别非常重要，因为创建后无法更改该服务级别。 需要在不同的容量池中创建新的卷，并复制数据。

请注意，您可以更改卷的存储配额和容量池的大小，而不会中断访问。

## <a name="create-storage-targets-in-the-cache"></a>在缓存中创建存储目标

设置 Azure NetApp 文件系统并创建 Azure HPC 缓存后，在指向文件系统卷的缓存中定义存储目标。

为 Azure NetApp 文件卷使用的每个 IP 地址创建一个存储目标。 IP 地址列在卷的装载说明页中。

如果多个卷共享同一个 IP 地址，则可以对所有卷使用一个存储目标。  

按照[Azure NetApp 文件文档中的装载说明](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)查找要使用的 IP 地址。

您还可以使用 Azure CLI 查找 IP 地址：

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Azure NetApp 文件系统上的导出名称具有单个路径组件。 不要尝试在 Azure NetApp 文件中为根``/``导出创建存储目标，因为该导出不提供文件访问权限。

对于这些存储目标的虚拟命名空间路径，没有特殊限制。

## <a name="mount-storage-targets"></a>装载存储目标

客户端计算机应装载缓存，而不是直接装载 Azure NetApp 文件卷。 按照装载 Azure [HPC 缓存中的](hpc-cache-mount.md)说明进行操作。

## <a name="next-steps"></a>后续步骤

* 阅读有关设置和使用 Azure [NetApp 文件的更多内容](../azure-netapp-files/index.yml)
* 有关规划和设置 Azure HPC 缓存系统以使用 Azure NetApp 文件的帮助，[请与支持 人员联系](hpc-cache-support-ticket.md)。

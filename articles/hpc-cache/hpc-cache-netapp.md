---
title: 使用 Azure HPC 缓存（预览版）和 Azure NetApp 文件
description: 如何使用 Azure HPC 缓存来改善对 Azure NetApp 文件中存储的数据的访问
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: rohogue
ms.openlocfilehash: 35d7a11c4f39f15c6b2f904df77b88b85c2208e5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954937"
---
# <a name="use-azure-hpc-cache-preview-with-azure-netapp-files"></a>将 Azure HPC 缓存（预览版）与 Azure NetApp 文件配合使用

可以使用[Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)作为 Azure HPC 缓存的存储目标。 本文介绍了这两个服务如何协同工作，并提供了设置这些服务的技巧。

Azure NetApp 文件将其 ONTAP 操作系统与 Microsoft Azure 的可伸缩性和速度相结合。 这种组合允许用户在不重写代码的情况下将已建立的工作流转移到云中

添加 Azure HPC 缓存组件可以通过在一个聚合的命名空间中提供多个 Azure NetApp 文件卷来改善文件访问。 它可以为位于不同服务区域的卷提供边缘缓存。 它还可以提高在较低层服务级别创建的卷的需求，从而节省成本。

## <a name="overview"></a>概述

若要使用 azure NetApp 文件系统作为 Azure HPC 缓存的后端存储，请按照此过程操作。

1. 根据[计划系统](#plan-your-azure-netapp-files-system)中的指南，创建 Azure NetApp 文件系统和卷。
1. 在需要文件访问的区域中创建 Azure HPC 缓存。 （请按照[创建 AZURE HPC 缓存](hpc-cache-create.md)中的说明进行操作。）
1. 在缓存中定义指向 Azure NetApp 文件卷的[存储目标](#create-storage-targets-in-the-cache)。 为用于访问卷的每个唯一 IP 地址创建一个缓存存储目标。
1. 让客户端[装载 AZURE HPC 缓存，](#mount-storage-targets)而不是直接装载 Azure NetApp 文件卷。

## <a name="plan-your-azure-netapp-files-system"></a>规划 Azure NetApp 文件系统

在规划 Azure NetApp 文件系统时，请注意本部分中的各项内容，确保可将其与 Azure HPC 缓存顺利集成。

另外，在创建用于 Azure HPC 缓存的卷之前，请阅读[Azure NetApp 文件文档](../azure-netapp-files/index.yml)。

### <a name="nfs-client-access-only"></a>仅限 NFS 客户端访问

Azure HPC 缓存目前仅支持 NFS 访问。 它不能与 SMB ACL 或 POSIX 模式位卷一起使用。

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Azure NetApp 文件的独占子网

Azure NetApp 文件对其卷使用一个委托子网。 其他资源不能使用该子网。 此外，虚拟网络中只能有一个子网用于 Azure NetApp 文件。 详细了解[Azure NetApp 文件网络规划指南](../azure-netapp-files/azure-netapp-files-network-topologies.md)。

### <a name="delegated-subnet-size"></a>委托子网大小

创建用于 Azure HPC 缓存的 Azure NetApp 文件系统时，请使用委托子网的最小大小。

指定的最小大小为网络掩码/28，提供16个 IP 地址。 实际上，Azure NetApp 文件仅使用其中三个可用的 IP 地址来访问卷。 这意味着，你只需在 Azure HPC 缓存中创建三个存储目标，即可涵盖所有卷。

如果委托子网太大，则 Azure NetApp 文件卷可能会使用比单个 Azure HPC 缓存实例可以处理的更多的 IP 地址。 单个缓存最多可以有10个存储目标。

Azure NetApp 文件文档中的快速入门示例文档使用 10.7.0.0/16 作为委托子网，这会给出一个太大的子网。

### <a name="capacity-pool-service-level"></a>容量池服务级别

为容量池选择服务级别时，请考虑工作流。 如果经常将数据写回到 Azure NetApp 文件卷，则在写回时间较慢的情况下，缓存的性能可能会受到限制。 为经常写入的卷选择较高的服务级别。

具有低服务级别的卷还可能在缓存预填充内容时，在任务开始时显示一些延迟。 缓存启动并运行后，使用一组良好的文件，延迟应该会难察觉。

提前规划容量池服务非常重要，因为在创建后不能更改它。 需要在不同的容量池中创建新卷，并复制数据。

请注意，你可以更改卷的存储配额和容量池的大小，而不会中断访问。

## <a name="create-storage-targets-in-the-cache"></a>在缓存中创建存储目标

设置 Azure NetApp 文件系统并创建 Azure HPC 缓存后，在缓存中定义指向文件系统卷的存储目标。

为 Azure NetApp 文件卷使用的每个 IP 地址创建一个存储目标。 此 IP 地址在卷的 "装入说明" 页中列出。

如果多个卷共享同一 IP 地址，则可以为所有卷使用一个存储目标。  

按照[Azure NetApp 文件文档中的装载说明](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)查找要使用的 IP 地址。

还可以通过 Azure CLI 查找 IP 地址：

```bash
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Azure NetApp 文件系统上的导出名称有一个路径组件。 请勿尝试为 Azure NetApp 文件中的根导出 ``/`` 创建存储目标，因为该导出不提供文件访问。

对于这些存储目标的虚拟命名空间路径没有特别的限制。

## <a name="mount-storage-targets"></a>装载存储目标

客户端计算机应装载缓存，而不是直接装载 Azure NetApp 文件卷。 按照[装载 AZURE HPC 缓存](hpc-cache-mount.md)中的说明进行操作。

## <a name="next-steps"></a>后续步骤

* 详细了解如何设置和使用[Azure NetApp 文件](../azure-netapp-files/index.yml)
* 若要帮助规划和设置 Azure HPC 缓存系统以使用 Azure NetApp 文件，请[联系支持](hpc-cache-support-ticket.md)人员。

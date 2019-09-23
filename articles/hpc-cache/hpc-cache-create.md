---
title: 创建 Azure HPC 缓存
description: 如何创建 Azure HPC 缓存实例
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: e1b69f17d964647944f23f4d16a0a1a5f112b60d
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037074"
---
# <a name="create-an-azure-hpc-cache"></a>创建 Azure HPC 缓存

使用 Azure 门户创建缓存。

![Azure 门户中的缓存概述的屏幕截图，底部带有“创建”按钮](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>定义基本详细信息

![Azure 门户中的“项目详细信息”页的屏幕截图](media/hpc-cache-create-basics.png)

在“项目详细信息”  中，选择将托管 Azure HPC 缓存的订阅和资源组。 请确保订阅位于[预览访问](hpc-cache-prereqs.md#azure-subscription)列表中。

在“服务详细信息”  中，设置缓存名称和其他属性：

* 位置 - 选择其中一个[支持的区域](hpc-cache-overview.md#region-availability)。
* 虚拟网络 - 可选择现有虚拟网络或创建新的虚拟网络。
* 子网 - 选择或创建至少包含 64 个 IP 地址 (/24) 的子网，它将仅用于 Azure HPC 缓存。

## <a name="set-cache-capacity"></a>设置缓存容量
<!-- change link in GUI -->

在“缓存”  页面上，必须设置 Azure HPC 缓存的容量。 此值确定缓存可以容纳的数据及其可以满足客户端请求的速度。 在公开预览版之后，容量也会影响缓存的成本。

缓存容量以每秒输入/输出操作数 (IOPS) 度量。 通过设置以下两个值选择容量：

* 缓存的最大数据传输速率（吞吐量）（GB/秒）
* 为缓存数据分配的存储量 (TB)

选择可用吞吐量值和缓存存储大小之一。 计算 IOPS 容量并在下方显示值选择器。

请记住，实际的数据传输速率取决于工作负荷、网络速度和存储目标的类型。 如果文件不在缓存中，或者文件被标记为过时，则该服务将使用一些吞吐量从后端存储提取该文件。 你选择的值将设置整个缓存的最大吞吐量，但并非所有吞吐量都可用于客户端请求。

对于缓存存储，Azure HPC 缓存会管理缓存和预加载哪些文件，以最大限度地提高缓存命中率。 当不经常访问缓存内容时，会不断评估缓存内容，并将文件移动到长期存储。 选择一种缓存存储大小，该大小可以轻松地保留活动的工作文件集，并具有额外的空间来存储元数据和其他开销。

![缓存大小调整页的屏幕截图](media/hpc-cache-create-iops.png)

## <a name="add-storage-targets"></a>添加存储目标

存储目标是缓存内容的后端长期存储。

你可以在创建缓存时定义存储目标，但也可以在门户中通过缓存页面的“配置”  部分中的链接来添加它们。

![存储目标页面的屏幕截图](media/hpc-cache-storage-targets-pop.png)

单击“添加存储目标”  链接以定义后端存储系统。 存储可以是 Azure Blob 容器或本地 NFS 系统。

可以定义最多 10 个不同的存储目标。

[添加存储](hpc-cache-add-storage.md)中提供了添加存储目标的分步说明。 Blob 存储或 NFS 导出的过程有所不同。

下面是一些提示： 

* 对于这两种存储类型，必须指定如何查找后端存储系统（NFS 地址或 Blob 容器名称）和面向客户端的命名空间路径。

* 创建 Blob 存储目标时，请确保缓存具有对存储帐户的访问权限，如[添加访问控制角色](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)中所述。 如果你不确定角色配置是否成功，请先创建缓存，然后添加 Blob 存储。

* 创建 NFS 存储目标时，请指定[使用情况模型](hpc-cache-add-storage.md#choose-a-usage-model)。 使用情况模型设置有助于缓存优化工作流。

## <a name="add-resource-tags-optional"></a>添加资源标记（可选）

在“标记”  页上，可以向 Azure HPC 缓存添加[资源标记](https://go.microsoft.com/fwlink/?linkid=873112)。 

## <a name="finish-creating-the-cache"></a>完成缓存创建

配置新缓存后，请单击“查看 + 创建”  选项卡。门户将验证你的选择，并允许你查看你的选择。 如果所有内容都正确，请单击“创建”  。 

缓存创建花费大约 10 分钟。 可在 Azure 门户的通知面板中跟踪进度。 

![门户中缓存创建“正在部署”和“通知”页的屏幕截图](media/hpc-cache-deploy-status.png)

创建完成后，将显示一条通知，其中包含指向新的 Azure HPC 缓存实例的链接，并且缓存将显示在订阅的**资源**列表中。 

![Azure 门户中的 Azure HPC 缓存实例的屏幕截图](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>后续步骤

缓存出现在“资源”  列表中后，可以装载此缓存以供客户端访问，使用它将工作集数据移动到新的 Azure Blob 存储目标，或定义其他数据源。

* [装载 Azure HPC 缓存](hpc-cache-mount.md)
* [将 Azure HPC 缓存的数据移至 Azure Blob 存储](hpc-cache-ingest.md)
* [添加存储目标](hpc-cache-add-storage.md)

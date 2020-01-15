---
title: 创建 Azure HPC 缓存
description: 如何创建 Azure HPC 缓存实例
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: rohogue
ms.openlocfilehash: 793a80e7019e72c1cb3087da02d5642639cb8d5e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647150"
---
# <a name="create-an-azure-hpc-cache"></a>创建 Azure HPC 缓存

使用 Azure 门户创建缓存。

![Azure 门户中的缓存概述的屏幕截图，底部带有“创建”按钮](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>定义基本详细信息

![Azure 门户中的“项目详细信息”页的屏幕截图](media/hpc-cache-create-basics.png)

在“项目详细信息”  中，选择将托管缓存的订阅和资源组。 请确保订阅位于[访问](hpc-cache-prereqs.md#azure-subscription)列表中。

在“服务详细信息”  中，设置缓存名称和其他属性：

* 位置 - 选择其中一个[支持的区域](hpc-cache-overview.md#region-availability)。
* 虚拟网络 - 可选择现有虚拟网络或创建新的虚拟网络。
* 子网 - 选择或创建至少包含 64 个 IP 地址 (/24) 的子网，它将仅用于此 Azure HPC 缓存实例。

## <a name="set-cache-capacity"></a>设置缓存容量
<!-- referenced from GUI - update aka.ms link if you change this header text -->

在“缓存”  页上，必须设置缓存的容量。 此处设置的值确定缓存可以容纳多少数据，以及为客户端请求提供服务的速度。

容量还会影响缓存的成本。

通过设置以下两个值选择容量：

* 缓存的最大数据传输速率（吞吐量）（GB/秒）
* 为缓存数据分配的存储量 (TB)

选择可用吞吐量值和缓存存储大小之一。

请记住，实际的数据传输速率取决于工作负荷、网络速度和存储目标的类型。 你选择的值设置整个缓存系统的最大吞吐量，但其中一些用于开销任务。 例如，如果客户端请求尚未存储在缓存中的文件，或者该文件被标记为过期，则缓存将使用其部分吞吐量从后端存储中提取该文件。

Azure HPC 缓存会管理缓存和预加载哪些文件，以最大限度地提高缓存命中率。 当不经常访问缓存内容时，会不断评估缓存内容，并将文件移动到长期存储。 选择一种缓存存储大小，该大小可以轻松地保留活动的工作文件集，并具有额外的空间来存储元数据和其他开销。

![缓存大小调整页的屏幕截图](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>添加资源标记（可选）

在“标记”  页上，可以向 Azure HPC 缓存实例添加[资源标记](https://go.microsoft.com/fwlink/?linkid=873112)。

## <a name="finish-creating-the-cache"></a>完成缓存创建

配置新缓存后，请单击“查看 + 创建”  选项卡。门户将验证你的选择，并允许你查看你的选择。 如果所有内容都正确，请单击“创建”  。

缓存创建花费大约 10 分钟。 可在 Azure 门户的通知面板中跟踪进度。

![门户中缓存创建“正在部署”和“通知”页的屏幕截图](media/hpc-cache-deploy-status.png)

创建完成后，将显示一条通知，其中包含指向新的 Azure HPC 缓存实例的链接，并且缓存将显示在订阅的**资源**列表中。
<!-- double check on notification -->

![Azure 门户中的 Azure HPC 缓存实例的屏幕截图](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>后续步骤

缓存出现在**资源**列表中后，定义存储目标以授予对数据源的缓存访问权限。

* [添加存储目标](hpc-cache-add-storage.md)

---
title: Azure HPC 缓存预览版概述
description: 介绍 Azure HPC 缓存，它是一种用于高性能计算的文件访问加速器解决方案
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 09/24/2019
ms.author: rohogue
ms.openlocfilehash: bfbbcd2d7d1bb44e260bedda54ca38ed6860ea67
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254478"
---
# <a name="what-is-azure-hpc-cache-preview"></a>什么是 Azure HPC 缓存？ （预览版）

Azure HPC 缓存可为高性能计算 (HPC) 任务加快对数据的访问。 通过在 Azure 中缓存文件，Azure HPC 缓存将云计算的可伸缩性带到现有工作流中。 此服务甚至可用于跨 WAN 链路（例如在本地数据中心网络附加存储 (NAS) 环境中）存储数据的工作流。

Azure HPC 缓存易于从 Azure 门户启动和监视。 现有的 NFS 存储或新的 Blob 容器可以成为其聚合命名空间的一部分，这使得客户端访问更简单，即使你更改后端存储目标也是如此。

## <a name="use-cases"></a>用例

Azure HPC 缓存最适合提高如下工作流的生产效率：

* 读取操作密集型文件访问工作流
* 存储在 NFS 可访问存储、Azure Blob 或两者中的数据
* 包含最多 75,000 个 CPU 核心的计算场

Azure HPC 缓存可添加到多个行业的各种工作流中。 大量计算机需要大规模访问一组文件且延迟较低的任何系统都将受益于此服务。 以下各部分给出了具体的示例。

### <a name="visual-effects-vfx-rendering"></a>视觉效果 (VFX) 渲染

在媒体和娱乐行业，Azure HPC 缓存可以加速时间关键型渲染项目的数据访问。 VFX 渲染工作流通常需要大量计算节点执行最后一分钟的处理。 这些工作流的数据通常位于本地 NAS 环境中。 Azure HPC 缓存可以在云中缓存该文件数据，以减少延迟并提高按需渲染的灵活性。

### <a name="life-sciences"></a>生命科学

许多生命科学工作流都可以从横向扩展文件缓存中获益。

想要将其基因组分析工作流移植到 Azure 中的研究所可以使用 Azure HPC 缓存轻松地对其进行转移。 由于缓存提供了 POSIX 文件访问，因此无需更改客户端即可在云中运行其现有的客户端工作流。

还可以利用 Azure HPC 缓存来改善辅助分析、药理仿真或 AI 驱动的图像分析等任务的效率。

### <a name="financial-services-analytics"></a>金融服务分析

Azure HPC 缓存部署可帮助加快定量分析计算、风险分析工作负荷和蒙特卡罗模拟，从而为金融服务公司提供更好的见解，以制定战略决策。

## <a name="region-availability"></a>上市区域

Azure HPC 缓存在以下 Azure 区域中提供：

* 美国东部
* 美国东部 2
* 北欧
* 西欧
* 东南亚
* 美国西部 2

有关最新可用性信息，请查看 [Azure HPC 缓存产品页](https://azure.microsoft.com/services/hpc-cache)。

## <a name="preview-availability"></a>预览版可用性

Azure HPC 缓存公共预览版受到限制，以确保服务质量。 通过填写[此表单](https://aka.ms/onboard-hpc-cache)来请求访问权限。 将订阅添加到访问列表后，可以创建测试缓存。

## <a name="next-steps"></a>后续步骤

* 阅读 [Azure HPC 缓存产品页](https://azure.microsoft.com/services/hpc-cache)，了解其功能的详细信息
* 了解产品[先决条件](hpc-cache-prereqs.md)
* 从 Azure 门户中[创建 Azure HPC 缓存](hpc-cache-create.md)

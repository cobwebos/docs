---
title: Azure Data Lake Storage Gen2 预览版分层命名空间
description: 说明 Azure Data Lake Storage Gen2 预览版分层命名空间的概念
services: storage
author: jamesbak
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 44eec21f4687d2df64c59d41cdb02c6ef2268f82
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528691"
---
# <a name="azure-data-lake-storage-gen2-preview-hierarchical-namespace"></a>Azure Data Lake Storage Gen2 预览版分层命名空间

一种关键机制，允许 Azure Data Lake Storage Gen2 预览版以对象存储规模和价格提供文件系统性能，是“分层命名空间”的新增内容。 此机制支持按照整理计算机上文件系统的相同方式，将帐户内的对象/文件集合整理成一个包含目录和嵌套子目录的层次结构。 启用分层命名空间后，Data Lake Storage Gen2 可通过分析引擎和框架所熟悉的文件系统语义，提供可扩展和具有成本效益的对象存储。

## <a name="the-benefits-of-the-hierarchical-namespace"></a>分层命名空间服务的优点

> [!NOTE]
> 在 Azure Data Lake Storage Gen2 的公共预览期间，下面列出的某些功能的可用性可能有所不同。 在预览计划期间发布新功能和区域时，这些信息将由我们的 Yammer 专门小组进行传达。  

以下优点适用于对 blob 数据实现分层命名空间的文件系统：

- **原子目录操作：** 通过采用在对象名称中嵌入斜杠 (/) 来表示路径段的惯用做法，使对象存储接近目录层次结构。 虽然此惯用做法适用于组织对象，但对于移动、重命名或删除目录等操作则没有帮助。 在没有真实目录的情况下，应用程序可能必须处理数百万个单独的 blob 才能完成目录级任务。 相比之下，分层命名空间可通过更新单个条目（父目录）来处理这些任务。 

    这种显著的优化对于许多大数据分析框架尤为重要。 Hive、Spark 等工具通常将输出写入临时位置，然后在作业结束时对该位置进行重命名。 如果没有分层命名空间，此重命名操作通常比分析过程本身需要更长时间。 作业延迟降低就等同于分析工作负荷的总拥有成本 (TCO) 降低。

- **熟悉的界面样式：** 文件系统被开发人员和用户及相关人员所熟知。 无需在迁移到云时学习新的存储范例，因为 Data Lake Storage Gen2 公开的文件系统界面与大型和小型计算机使用相同的范例。

对象存储以前不支持分层命名空间，原因之一是分层命名空间的规模有限。 但是，Data Lake Storage Gen2 分层命名空间以线性方式扩展，并且不会降低数据容量或性能。

## <a name="when-to-enable-the-hierarchical-namespace"></a>何时启用分层命名空间

对于为操作目录的文件系统设计的存储工作负荷，建议启用分层命名空间。 这包括主要用于分析处理的所有工作负荷。 启用分层命名空间对于需要高度整理的数据集同样有益处。

启用分层命名空间的原因由 TCO 分析确定。 一般而言，由于存储加速改善工作负荷延迟，所需的计算资源时间将会缩短。 由于分层命名空间启用的原子目录操作，许多工作负荷的延迟可能会得到改善。 在许多工作负荷中，计算资源占总成本的 85% 以上，因此即使适度减少工作负荷延迟，也相当于大量节省了 TCO。 即使启用分层命名空间会增加存储成本，但由于降低了计算成本，TCO 仍然会降低。

## <a name="when-to-disable-the-hierarchical-namespace"></a>何时禁用分层命名空间

某些对象存储工作负荷可能无法通过启用分层命名空间获得任何益处。 此类工作负荷的示例包括备份、图像存储和其他应用程序，其中对象组织与对象本身分开存储（例如，存储在单独的数据库中）。

> [!NOTE]
> 使用预览版时，如果启用分层命名空间，则 Blob 和 Data Lake Storage Gen2 REST API 之间将不存在数据或操作的互操作性。 预览期间将添加此功能。

## <a name="next-steps"></a>后续步骤

- [创建存储帐户](./quickstart-create-account.md)
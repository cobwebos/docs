---
title: Azure Data Lake Storage Gen2 分层命名空间
description: 描述 Azure 数据湖存储 Gen2 的分层命名空间的概念
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6ce94590a1d0de6941c27d972bdd1c4194080e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153071"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Azure Data Lake Storage Gen2 分层命名空间

一种关键机制，允许 Azure Data Lake Storage Gen2 以对象存储规模和价格提供文件系统性能，是**分层命名空间**的新增内容。 此机制支持按照整理计算机上文件系统的相同方式，将帐户内的对象/文件集合整理成一个包含目录和嵌套子目录的层次结构。 启用分层命名空间后，存储帐户将能够提供对象存储的可扩展性和成本效益，并且具有分析引擎和框架熟悉的文件系统语义。

## <a name="the-benefits-of-a-hierarchical-namespace"></a>分层命名空间的好处

以下优点适用于对 blob 数据实现分层命名空间的文件系统：

- **原子目录操作：** 通过采用在对象名称中嵌入斜杠 (/) 来表示路径段的惯用做法，使对象存储接近目录层次结构。 虽然此惯用做法适用于组织对象，但对于移动、重命名或删除目录等操作则没有帮助。 在没有真实目录的情况下，应用程序可能必须处理数百万个单独的 blob 才能完成目录级任务。 相比之下，分层命名空间通过更新单个条目（父目录）来处理这些任务。

    这种显著的优化对于许多大数据分析框架尤为重要。 Hive、Spark 等工具通常将输出写入临时位置，然后在作业结束时对该位置进行重命名。 如果没有分层命名空间，此重命名通常比分析过程本身需要更长的时间。 作业延迟降低就等同于分析工作负荷的总拥有成本 (TCO) 降低。

- **熟悉的界面样式：** 文件系统被开发人员和用户及相关人员所熟知。 无需在迁移到云时学习新的存储范例，因为 Data Lake Storage Gen2 公开的文件系统界面与大型和小型计算机使用相同的范例。

对象存储以前不支持分层命名空间，原因之一是分层命名空间会限制规模。 但是，Data Lake Storage Gen2 分层命名空间以线性方式扩展，并且不会降低数据容量或性能。

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>决定是否启用分层命名空间

在帐户上启用分层命名空间后，无法将其还原回平面命名空间。 因此，请考虑根据对象存储工作负荷的性质启用分层命名空间是否有意义。

某些工作负载可能无法通过启用分层命名空间获得任何好处。 示例包括备份、图像存储和其他应用程序，其中对象组织与对象本身分开存储（例如，存储在单独的数据库中）。 

此外，虽然对 Blob 存储功能和 Azure 服务生态系统的支持继续增加，但某些功能和 Azure 服务在具有分层命名空间的帐户中尚未受支持。 请参阅[已知问题](data-lake-storage-known-issues.md)。 

通常，我们建议您为存储工作负载打开分层命名空间，该命名空间专为操作目录的文件系统而设计。 这包括主要用于分析处理的所有工作负荷。 需要高度组织的数据集也会通过启用分层命名空间而受益。

启用分层命名空间的原因由 TCO 分析确定。 一般而言，由于存储加速改善工作负荷延迟，所需的计算资源时间将会缩短。 由于分层命名空间启用了原子目录操作，许多工作负载的延迟可能会得到改善。 在许多工作负荷中，计算资源占总成本的 85% 以上，因此即使适度减少工作负荷延迟，也相当于大量节省了 TCO。 即使在启用分层命名空间会增加存储成本的情况下，TCO 也会由于计算成本的降低而降低。

要分析具有平坦分层命名空间的帐户与分层命名空间之间的数据存储价格、事务价格和存储容量预留定价的差异，请参阅[Azure 数据湖存储 Gen2 定价](https://azure.microsoft.com/pricing/details/storage/data-lake/)。

## <a name="next-steps"></a>后续步骤

- [创建存储帐户](./data-lake-storage-quickstart-create-account.md)

---
title: Azure Data Lake Storage Gen2 预览版简介
description: 提供 Azure Data Lake Storage Gen2 预览版的概述
services: storage
author: jamesbak
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 51f38cf7ade01b58ad5ce7925af5546d1a4f1a0c
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525376"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Azure Data Lake Storage Gen2 预览版简介

Azure Data Lake Storage Gen2 预览版是一组致力于进行大数据分析的功能，基于[Azure Blob 存储](../blobs/storage-blobs-introduction.md)而构建。 它允许你使用文件系统和对象存储范例与数据进行交互。 这使得 Data Lake Storage Gen2 成为唯一基于云的多模式存储服务，允许你从你所有数据中提取分析值。

Data Lake Storage Gen2 具有完整的分析数据生命周期所需的所有功能。 这是通过融合我们两个现有存储服务的功能而产生的。 [Azure Data Lake Storage Gen1](../../data-lake-store/index.md) 的功能（如文件系统语义、文件级安全性和规模）可与低成本的分层存储、高可用性/灾难恢复功能以及 [Azure Blob 存储](../blobs/storage-blobs-introduction.md)中的大量 SDK/工具生态系统结合使用。 在 Data Lake Storage Gen2 中，在添加针对分析工作负载优化的文件系统接口的优点的同时，还保留了对象存储的所有功能。

## <a name="designed-for-enterprise-big-data-analytics"></a>专为企业大数据分析而设计

Data Lake Storage Gen2 是用于在 Azure 上生成企业 data lake (EDL) 的基础存储服务。 Data Lake Storage Gen2 从一开始就设计为存储数千万亿字节的信息，同时保持数百千兆位的吞吐量，为你提供一种管理大量数据的简单办法。

Data Lake Storage Gen2 的一个基本功能是在 Blob 存储服务中添加一个[分层命名空间](./namespace.md)，该存储服务将对象/文件组织成用于执行数据访问的目录层次结构。 分层命名空间还使 Data Lake Storage Gen2 能够同时支持对象存储和文件系统范例。 例如，常见的对象存储命名约定在名称中使用斜杠来模拟分层文件夹结构。 这种结构在 Data Lake Storage Gen2 中得以真正实现。 诸如重命名或删除目录之类的操作在目录上成为单个原子元数据操作，而不是枚举或处理共享目录名称前缀的所有对象。

过去，基于云的分析必须在性能、管理和安全性方面做出妥协。 Data Lake Storage Gen2 通过以下方式解决这些方面中的每个问题：

- 优化了性能，因为你不需要将复制或转换数据作为分析的先决条件。 分层命名空间极大地提高了目录管理操作的性能，从而提高了整体作业性能。

- 管理更为容易，因为你可以通过目录和子目录来组织和操作文件。

- 由于 Data Lake Storage Gen2 基于低成本的 [Azure Blob 存储](../blobs/storage-blobs-introduction.md)而构建，因此，可以实现成本效益。 这些新增功能进一步降低了在 Azure 上运行大数据分析的总拥有成本。

## <a name="key-features-of-data-lake-storage-gen2"></a>Data Lake Storage Gen2 的主要功能

> [!NOTE]
> 在 Data Lake Storage Gen2 的公共预览版期间，下面列出的某些功能可能会有所不同。 在预览计划期间发布新功能和区域时，将告知此信息。
> [注册](https://aka.ms/adlsgen2signup) Data Lake Storage Gen2 的公开预览版。  

- Hadoop 兼容访问：Data Lake Storage Gen2 允许你管理和访问数据，就像在 [Hadoop 分布式文件系统 (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) 中一样。 新的 [ABFS 驱动程序](./abfs-driver.md)可在所有 Apache Hadoop 环境中使用，包括 [Azure HDInsight](../../hdinsight/index.yml) 和 [Azure Databricks](../../azure-databricks/index.yml)，以访问 Data Lake Storage Gen2 中存储的数据。

- 多协议和多模式数据访问：Data Lake Storage Gen2 被视为多模式存储服务，因为它为相同数据同时提供对象存储和文件系统接口。 这是通过提供能够访问相同数据的多个协议终结点来实现的。 

    与其他分析解决方案不同，存储在 Data Lake Storage Gen2 的数据在运行各种分析工具之前无需移动或转换。 你可以通过传统的 [Blob 存储 API](../blobs/storage-blobs-introduction.md) 访问数据（例如：通过[事件中心捕获](../../event-hubs/event-hubs-capture-enable-through-portal.md)来引入数据）并同时使用 HDInsight 或 Azure Databricks 来处理该数据。 

- 成本效益：Data Lake Storage Gen2 具有低成本的存储容量和事务。 随着数据在其整个生命周期中的转换，记帐费率变化通过诸如 [Azure Blob 存储生命周期](../common/storage-lifecycle-managment-concepts.md)的内置功能使成本保持在最低水平。

- 使用 Blob 存储工具、框架和应用：Data Lake Storage Gen2 可以继续使用目前适用于 Blob 存储的各种工具、框架和应用程序。

- **优化的驱动程序**：`abfs` 驱动程序专门针对大数据分析而进行[优化](./abfs-driver.md)。 相应的 REST API 通过 `dfs` 终结点、`dfs.core.windows.net` 进行显示。

## <a name="scalability"></a>可伸缩性

按照设计，无论是通过 Data Lake Storage Gen2 还是 Blob 存储接口进行访问，Azure 存储都可自如缩放。 它可以存储和处理许多百亿亿字节的数据。 这种存储量可用于在每秒高级别的输入/输出操作 (IOPS) 下以每秒千兆位 (Gbps) 的速度测量的吞吐量。 除持久性之外，以几乎恒定的每个请求延迟执行处理，这些延迟是在服务、帐户和文件级别上测量的。

## <a name="cost-effectiveness"></a>成本效益

基于 Azure Blob 存储生成 Data Lake Storage Gen2 的多个好处之一是存储容量和事务的[低成本](https://azure.microsoft.com/pricing/details/storage)。 与其他云存储服务不同，Data Lake Storage Gen2 降低了成本，因为在执行分析之前不需要移动或转换数据。

此外，[分层命名空间](./namespace.md)等功能可显著提高许多分析作业的整体性能。 这一性能方面的提升意味着你需要较少的计算能力来处理相同数量的数据，从而降低端到端分析作业的总拥有成本 (TCO)。

## <a name="next-steps"></a>后续步骤

以下文章介绍 Data Lake Storage Gen2 的一些主要概念，并详细介绍如何存储、访问、管理数据以及从数据中获取见解：

* [分层命名空间](./namespace.md)
* [创建存储帐户](./quickstart-create-account.md)
* [创建采用 Azure Data Lake Storage Gen2 的 HDInsight 群集](./quickstart-create-connect-hdi-cluster.md)
* [在 Azure Databricks 中使用 Azure Data Lake Storage Gen2 帐户](./quickstart-create-databricks-account.md) 
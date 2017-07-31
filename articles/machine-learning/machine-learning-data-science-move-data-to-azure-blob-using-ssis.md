---
title: "使用 SSIS 连接器将数据移入或移出 Azure Blob 存储 | Microsoft Docs"
description: "使用 SSIS 连接器将数据移入或移出 Azure Blob 存储。"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 96a1b5fb-34d1-4b9b-8d99-2bb8289e0398
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 303d7b06d259bc42c8093fb20a0e4a55410e28f5
ms.contentlocale: zh-cn
ms.lasthandoff: 07/11/2017


---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>使用 SSIS 连接器将数据移入或移出 Azure Blob 存储
[用于 Azure 的 SQL Server Integration Services 功能包](https://msdn.microsoft.com/library/mt146770.aspx)提供了可用于连接到 Azure，在 Azure 和本地数据源之间传输数据以及处理存储在 Azure 中的数据的组件。

[!INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

客户将本地数据移动到云中后，他们便可以从任何 Azure 服务访问数据，以利用 Azure 技术套件的完整功能。 例如，可以在 Azure 机器学习中或 HDInsight 集群上使用它。

这通常是进行 [SQL](machine-learning-data-science-process-sql-walkthrough.md) 和 [HDInsight](machine-learning-data-science-process-hive-walkthrough.md) 演练的第一步。

若要深入了解使用 SSIS 完成混合数据集成方案中常见的业务需求的规范方案讨论，请参阅[Doing more with SQL Server Integration Services Feature Pack for Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx)（使用用于 Azure 的 SQL Server Integration Services 功能包执行更多操作）博客。

> [!NOTE]
> 有关 Azure Blob 存储的完整介绍，请参阅 [Azure Blob 基本知识](../storage/storage-dotnet-how-to-use-blobs.md)和 [Azure Blob 服务](https://msdn.microsoft.com/library/azure/dd179376.aspx)。
> 
> 

## <a name="prerequisites"></a>先决条件
若要执行本文所述任务，必须设置 Azure 订阅和 Azure 存储帐户。 上传或下载数据之前，必须知道 Azure 存储帐户名和帐户密钥。

* 若要设置 **Azure 订阅**，请参阅[免费试用一个月](https://azure.microsoft.com/pricing/free-trial/)。
* 若要深入了解创建**存储帐户**的说明，以及获取帐户和密钥信息，请参阅[关于 Azure 存储帐户](../storage/storage-create-storage-account.md)。

若要使用 **SSIS 连接器**，则必须下载：

* **SQL Server 2014 或 2016 Standard（或更高版本）**：安装包括 SQL Server Integration Services。
* **用于 Azure 的 Microsoft SQL Server 2014 或 2016 Integration Services 功能包**：可以从 [SQL Server 2014 Integration Services](http://www.microsoft.com/download/details.aspx?id=47366) 和 [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) 页面分别下载这两个功能包。

> [!NOTE]
> SSIS 随 SQL Server 一起安装，但并不包括在 Express 版本中。 若要深入了解 SQL Server 各种版本中包含哪些应用程序，请参阅 [SQL Server Editions](http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)（SQL Server 版本）
> 
> 

有关 SSIS 的培训资料，请参阅 [Hands On Training for SSIS](http://www.microsoft.com/download/details.aspx?id=20766)（SSIS 培训指导）

若要深入了解如何使用 SISS 启动并运行以生成简单的提取、转换和加载 (ETL) 包，请参阅 [SSIS教程：创建简单的 ETL 包](https://msdn.microsoft.com/library/ms169917.aspx)。

## <a name="download-nyc-taxi-dataset"></a>下载 NYC 出租车数据集
此处所述的示例使用公开发布的数据集 - [NYC 出租车行程](http://www.andresmh.com/nyctaxitrips/)。 此数据集包含 2013 年纽约市内约 1.73 亿次出租车行程。 有两种类型的数据：行程详细信息数据和费用数据。 因为每个月都有一个文件，因此共有 24 个文件，每个未压缩的文件大约有 2GB。

## <a name="upload-data-to-azure-blob-storage"></a>将数据上传到 Azure Blob 存储
若要使用 SSIS 功能包将数据从本地移动到 Azure Blob 存储，请使用[**Azure Blob 上传任务**](https://msdn.microsoft.com/library/mt146776.aspx)的实例，如下所示：

![configure-data-science-vm](./media/machine-learning-data-science-move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

以下是任务使用的参数：

| 字段 | 说明 |
| --- | --- |
| **AzureStorageConnection** |指定现有 Azure 存储连接管理器或新建一个 Azure 存储连接管理器，该管理器引用指向 blob 文件的托管位置的 Azure 存储帐户。 |
| **BlobContainer** |指定将上传的文件保存为 blob 的 blob 容器名称。 |
| **BlobDirectory** |指定作为块 blob 存储的上传文件所在的 blob 目录。 该 blob 目录是一个虚拟层次结构。 如果 blob 已存在，其将被替代。 |
| **LocalDirectory** |指定包含要上传的文件的本地目录。 |
| **FileName** |指定名称筛选器以选择具有指定名称模式的文件。 例如，MySheet\*.xls\* 包括 MySheet001.xls 和 MySheetABC.xlsx 等文件 |
| **TimeRangeFrom/TimeRangeTo** |指定时间范围筛选器。 包括在 *TimeRangeFrom* 之后且在 *TimeRangeTo* 之前所修改的文件。 |

> [!NOTE]
> **AzureStorageConnection** 凭据必须正确，且在尝试进行传输之前，**BlobContainer** 必须存在。
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>从 Azure Blob 存储下载数据
要使用 SSIS 将数据从 Azure Blob 存储下载到本地存储，请使用 [Azure Blob 上传任务](https://msdn.microsoft.com/library/mt146779.aspx)的实例。

## <a name="more-advanced-ssis-azure-scenarios"></a>更高级的 SSIS-Azure 方案
SSIS 功能包能够通过将任务一起打包来处理更复杂的流。 例如，blob 数据可以直接传输到 HDInsight 群集，可将此群集的输出下载回 blob，然后再下载到本地存储。 SSIS 可使用附加的 SSIS 连接器在 HDInsight 群集上运行 Hive 和 Pig 作业：

* 若要使用 SSIS 在 Azure HDInsight 群集上运行 Hive 脚本，请使用 [Azure HDInsight Hive 任务](https://msdn.microsoft.com/library/mt146771.aspx)。
* 若要使用 SSIS 在 Azure HDInsight 群集上运行 Pig 脚本，请使用 [Azure HDInsight Pig 任务](https://msdn.microsoft.com/library/mt146781.aspx)。



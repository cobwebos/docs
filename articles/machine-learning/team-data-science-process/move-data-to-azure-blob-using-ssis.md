---
title: 使用 SSIS 连接器移动 Blob 存储数据 - Team Data Science Process
description: 了解如何使用适用于 Azure SQL Server Integration Services 功能包将数据移入或移出 Azure Blob 存储。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 77bfd9d5bcae7bedd673354e32464d5f59bdc9b4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720865"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>使用 SSIS 连接器将数据移入或移出 Azure Blob 存储
[用于 Azure 的 SQL Server Integration Services 功能包](https://msdn.microsoft.com/library/mt146770.aspx)提供了可用于连接到 Azure，在 Azure 和本地数据源之间传输数据以及处理存储在 Azure 中的数据的组件。

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

客户将本地数据移入云中后，便可以从任何 Azure 服务访问其数据，以充分利用 Azure 技术套件的全部功能。 例如，可以在 Azure 机器学习或 HDInsight 群集上使用这些数据。

有关使用这些 Azure 资源的示例，请查看[SQL](sql-walkthrough.md)和[HDInsight](hive-walkthrough.md)演练。

若要深入了解使用 SSIS 完成混合数据集成方案中常见的业务需求的规范方案讨论，请参阅[Doing more with SQL Server Integration Services Feature Pack for Azure](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx)（使用用于 Azure 的 SQL Server Integration Services 功能包执行更多操作）博客。

> [!NOTE]
> 有关 Azure Blob 存储的完整介绍，请参阅 [Azure Blob 基本知识](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)和 [Azure Blob 服务](https://msdn.microsoft.com/library/azure/dd179376.aspx)。
> 
> 

## <a name="prerequisites"></a>必备条件
若要执行本文中所述的任务，必须设置 Azure 订阅和 Azure 存储帐户。 需要 Azure 存储帐户名称和帐户密钥来上传或下载数据。

* 若要设置 **Azure 订阅**，请参阅[免费试用一个月](https://azure.microsoft.com/pricing/free-trial/)。
* 有关创建**存储帐户**以及获取帐户和密钥信息的说明，请参阅[关于 Azure 存储帐户](../../storage/common/storage-create-storage-account.md)。

若要使用 **SSIS 连接器**，则必须下载：

* **SQL Server 2014 或 2016 Standard（或更高版本）** ：安装包括 SQL Server Integration Services。
* **适用于 Azure 的 Microsoft SQL Server 2014 或 2016 Integration Services 功能包**：可以分别从[SQL Server 2014 Integration Services](https://www.microsoft.com/download/details.aspx?id=47366)和[SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492)页下载这些连接器。

> [!NOTE]
> SSIS 随 SQL Server 一起安装，但并不包括在 Express 版本中。 若要深入了解 SQL Server 各种版本中包含哪些应用程序，请参阅 [SQL Server Editions](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)（SQL Server 版本）
> 
> 

有关 SSIS 的培训资料，请参阅 [Hands On Training for SSIS](https://www.microsoft.com/sql-server/training-certification)（SSIS 培训指导）

若要深入了解如何使用 SISS 启动并运行以生成简单的提取、转换和加载 (ETL) 包，请参阅 [SSIS教程：创建简单的 ETL 包](https://msdn.microsoft.com/library/ms169917.aspx)。

## <a name="download-nyc-taxi-dataset"></a>下载 NYC 出租车数据集
此处所述的示例使用公开发布的数据集 - [NYC 出租车行程](https://www.andresmh.com/nyctaxitrips/)。 此数据集包含 2013 年纽约市内约 1.73 亿次出租车行程。 有两种类型的数据：行程详细信息数据和费用数据。 每个月有一个文件，每个文件有24个文件，每个文件大约有 2 GB 未压缩。

## <a name="upload-data-to-azure-blob-storage"></a>将数据上传到 Azure Blob 存储
要使用 SSIS 功能包将数据从本地移动到 Azure Blob 存储，使用[**Azure Blob 上传任务**](https://msdn.microsoft.com/library/mt146776.aspx)的实例，如下所示：

![configure-data-science-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

以下是任务使用的参数：

| 字段 | 说明 |
| --- | --- |
| AzureStorageConnection |指定一个现有的 Azure 存储连接管理器，或创建一个新的连接管理器，用于引用指向在其中托管 blob 文件的 Azure 存储帐户。 |
| **BlobContainer** |指定将上载的文件作为 blob 保留的 blob 容器的名称。 |
| **BlobDirectory** |指定将上载的文件作为块 blob 存储的 blob 目录。 该 blob 目录是一个虚拟层次结构。 如果 blob 已存在，其会被替代。 |
| **LocalDirectory** |指定包含要上传的文件的本地目录。 |
| **FileName** |指定名称筛选器以选择具有指定名称模式的文件。 例如，MySheet\*.xls\* 包括 MySheet001.xls 和 MySheetABC.xlsx 等文件 |
| **TimeRangeFrom/TimeRangeTo** |指定时间范围筛选器。 将包括在 *TimeRangeFrom* 之后以及 *TimeRangeTo* 之前修改的文件。 |

> [!NOTE]
> **AzureStorageConnection** 凭据必须正确，且在尝试进行传输之前，**BlobContainer** 必须存在。
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>从 Azure Blob 存储下载数据
要使用 SSIS 将数据从 Azure Blob 存储下载到本地存储，请使用 [Azure Blob 下载任务](https://msdn.microsoft.com/library/mt146779.aspx)的实例。

## <a name="more-advanced-ssis-azure-scenarios"></a>更高级的 SSIS-Azure 方案
SSIS 功能包能够通过将任务一起打包来处理更复杂的流。 例如，blob 数据可以直接传输到 HDInsight 群集，可将此群集的输出下载回 blob，再下载到本地存储。 SSIS 可使用附加的 SSIS 连接器在 HDInsight 群集上运行 Hive 和 Pig 作业：

* 若要使用 SSIS 在 Azure HDInsight 群集上运行 Hive 脚本，请使用 [Azure HDInsight Hive 任务](https://msdn.microsoft.com/library/mt146771.aspx)。
* 若要使用 SSIS 在 Azure HDInsight 群集上运行 Pig 脚本，请使用 [Azure HDInsight Pig 任务](https://msdn.microsoft.com/library/mt146781.aspx)。


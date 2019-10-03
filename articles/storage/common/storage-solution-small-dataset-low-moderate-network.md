---
title: 适用于小型数据集以及中低速网络带宽的 Azure 数据传输选项 | Microsoft Docs
description: 了解在环境中有中低速网络带宽且正在计划传输小型数据集时，如何选择数据传输的 Azure 解决方案。
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: 3e6f4f3eb312f0d4d96a008c0944a9608d0bf4a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60397271"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>适用于小型数据集以及中低速网络带宽的数据传输
 
本文概述了在环境中有中低速网络带宽并且正在计划传输小型数据集时的数据传输解决方案。 本文还介绍了针对此情况的推荐数据传输选项和相应的关键功能矩阵。

若要查看所有可用数据传输选项的概述，请转到[选择一个 Azure 数据传输解决方案](storage-choose-data-transfer-solution.md)。

## <a name="scenario-description"></a>方案描述

小型数据集指的是 GB 到 TB 级的数据大小。 中低速网络带宽表示 45 Mbps（数据中心的 T3 连接）到 1 Gbps。

- 若只传输少量文件且不需要自动执行数据传输，请考虑使用带图形界面的工具。
- 如果熟悉系统管理，请考虑使用命令行或编程/脚本工具。

## <a name="recommended-options"></a>推荐选项

此方案中推荐的选项包括：

- 图形界面工具  ，例如 Azure 门户中的 Azure 存储资源管理器和 Azure 存储。 这些工具提供了一种查看数据和快速传输几个文件的简便方法。

    - Azure 存储资源管理器  - 此跨平台工具可用于管理 Azure 存储帐户的内容。 使用它可以上传、下载和管理 Blob、文件、队列、表以及 Azure Cosmos DB 实体。 将它与 Blob 存储配合使用可以管理 Blob 和文件夹，以及在本地文件系统与 Blob 存储之间，或者在存储帐户之间上传和下载 Blob。
    - Azure 门户  - Azure 门户中的 Azure 存储提供一个基于 Web 的界面，可用于浏览文件以及一次上传一个新文件。 如果你希望不必安装任何工具或发出命令就能快速浏览文件，或者只是要上传少量的新文件，则此选项很合适。

- 脚本/编程工具  ，例如 AzCopy/PowerShell/Azure CLI 和 Azure 存储 REST API。

    - AzCopy  - 使用此命令行工具在保证最佳性能的同时轻松向/从 Azure Blob、文件和表存储复制数据。 AzCopy 支持并发度和并行度，并且可以在复制操作中断后进行恢复。
    - Azure PowerShell  - 对于熟悉系统管理的用户，请使用 Azure PowerShell 中的 Azure 存储模块来传输数据。
    - Azure CLI  - 使用此跨平台工具，可管理 Azure 服务以及将数据上传到 Azure 存储。
    - Azure 存储 REST API/SDK  - 生成应用程序时，可以针对 Azure 存储 REST API/SDK 开发应用程序，并使用以多种语言提供的 Azure 客户端库。


## <a name="comparison-of-key-capabilities"></a>关键功能比较

下表汇总了各项关键功能方面的差异。

| Feature | Azure 存储资源管理器 | Azure 门户 | AzCopy<br>Azure PowerShell<br>Azure CLI | Azure 存储 REST API 或 SDK |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| 可用性 | 下载并安装 <br>独立工具 | Azure 门户中基于 Web 的浏览工具 | 命令行工具 |在 .NET、Java、Python、JavaScript、C++、Go、Ruby 和 PHP 中的可编程接口 |
| 图形界面 | 是 | 是 | 否 | 否 |
| 支持的平台 | Windows、Mac、Linux | 基于 Web |Windows、Mac、Linux |所有平台 |
| 允许对 blob 和文件夹进行的<br>Blob 存储操作 | 上载<br>下载<br>管理 | 上载<br>下载<br>管理 |上载<br>下载<br>管理 | 是，可自定义 |
| 允许对文件和文件夹进行的<br>Data Lake Gen1 存储操作 | 上载<br>下载<br>管理 | 否 |上载<br>下载<br>管理                   | 否 |
| 允许对文件和目录进行的<br>文件存储操作 | 上载<br>下载<br>管理 | 上载<br>下载<br>管理   |上载<br>下载<br>管理 | 是，可自定义 |
| 允许对表格进行的<br>表存储操作 |管理 | 否 |AzCopy v7 中的表支持 |是，可自定义|
| 允许的队列存储 | 管理 | 否  |否 | 是，可自定义|


## <a name="next-steps"></a>后续步骤

- 了解如何[使用 Azure 存储资源管理器传输数据](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer)。
- [使用 AzCopy 传输数据](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)


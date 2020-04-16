---
title: 使用一键式引入将数据引入到 Azure 数据资源管理器中
description: 概述如何使用一键式引入轻松地将数据引入（载入）Azure 数据资源管理器。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 5bde63427ce76f14832551864bbf2c3d8e015fd6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521590"
---
# <a name="what-is-one-click-ingestion"></a>什么是一键式引入？ 

一键式引入可让你快速引入数据，并根据 Azure 数据资源管理器中的数据源自动推荐表和映射结构。 

使用 Azure 数据资源管理器 Web UI 可以从存储（Blob 文件）、本地文件或容器（最多包含 10,000 个 Blob）中引入数据。 还可以在容器中定义事件网格，以实现连续引入。 可将 JSON、CSV 和[其他格式](#file-formats)的数据引入到现有的表或新表中。 一键式引入可以根据数据源推荐新表和表映射的结构，并提供直观的平台，用于调整现有表和表映射的表结构。 在几分钟内，一键式引入就能将数据引入到表中。

首次引入数据时，或者在你不熟悉自己数据的架构时，一键式引入特别有用。

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* 创建 [Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)。
* 登录到 [Azure 数据资源管理器 Web UI](https://dataexplorer.azure.com/) 并[添加与群集的连接](/azure/data-explorer/web-query-data#add-clusters)。

## <a name="file-formats"></a>文件格式

一键式引入支持从以下任意格式的源数据引入新表：
* JSON
* CSV
* TSV
* SCSV
* SOHSV
* TSVE
* PSV

## <a name="one-click-ingestion-wizard"></a>一键式引入向导

一键式引入向导可以引导你完成一键式引入过程。 

> [!Note]
> 本部分介绍该向导的常规知识。 选择哪些选项取决于是要引入到新表还是现有表。 有关详细信息，请参阅：
    > * 引入到[新表](one-click-ingestion-new-table.md)
    > * 引入到[现有表](one-click-ingestion-existing-table.md) 
    
1. 若要访问该向导，请右键单击 Azure 数据资源管理器 Web UI 左侧菜单中的“数据库”或“表”行，然后选择“引入新数据(预览版)”。   

    ![在 Web UI 中选择一键式引入](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   

1. 该向导会引导你配置以下选项：
       * 引入到[现有表](one-click-ingestion-existing-table.md)
       * 引入到[新表](one-click-ingestion-new-table.md)
       * 数据引入位置：* Blob 存储      * 本地文件      * 容器
       * 输入样本大小：1 到 10,000 行（仅限容器中的行）
       
1. 成功选择数据源后，会显示数据的预览。 
    如果从容器引入数据，可以筛选数据，以便仅引入具有特定前缀或扩展名的文件。 例如，你可能只想引入文件名以单词 Europe 开头的文件，或者只想引入扩展名为 .json 的文件。   

1. 单击“编辑架构”。  如果将数据引入特定的表中，可将源列映射到目标列，并确定是否要包含列名称。

1. 启动数据引入过程。

> [!Note]
> 如果数据源是容器，请注意，Azure 数据资源管理器的数据引入聚合（批处理）策略旨在优化引入过程。 默认情况下，该策略配置为 5 分钟或 500 MB 的数据，因此你可能会遇到延迟。 有关聚合选项，请参阅[批处理策略](/azure/kusto/concepts/batchingpolicy)。 从其他源引入数据时，引入会立即生效。

## <a name="next-steps"></a>后续步骤

* 确定是否要使用一键式引入将数据引入[现有表](one-click-ingestion-existing-table.md)或[新表](one-click-ingestion-new-table.md)
* [在 Azure 数据资源管理器 Web UI 中查询数据](/azure/data-explorer/web-query-data)
* [使用 Kusto 查询语言编写 Azure 数据资源管理器的查询](/azure/data-explorer/write-queries)
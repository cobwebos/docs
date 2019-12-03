---
title: 使用一键式引入将数据引入 Azure 数据资源管理器
description: 了解如何只需使用一键式引入即可将数据引入（加载）到 Azure 数据资源管理器。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: c4ee4ed81cd4cc443a8f412462a5a7f204c91898
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688194"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>使用一键式引入将数据引入 Azure 数据资源管理器

本文介绍如何使用一键式引入快速引入 json 格式的新表或本地文件中的 csv 格式的新表或 Azure 数据资源管理器中的新表。 使用直观的向导，在几分钟内，你的数据是引入的，你可以编辑表，并使用 Web UI 运行查询。

## <a name="prerequisites"></a>必备组件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* 登录到[应用程序](https://dataexplorer.azure.com/)。
* 创建[Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)
* 登录到[WEB UI](https://dataexplorer.azure.com/)并[添加到群集的连接](/azure/data-explorer/web-query-data#add-clusters)
* Azure 存储中的数据源。

## <a name="ingest-new-data"></a>引入新数据

1. 右键单击 Web UI 的左侧菜单中的 "*数据库*" 或 "*表*" 行，然后选择 "引入**新数据" （预览）**

    ![在 web UI 中选择一次单击引入](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. 在 "引入**新数据（预览）** " 窗口的 "**源**" 选项卡中，完成**项目详细信息**：

    * **表**：从下拉列表中选择现有表名称，或选择 "**新建**" 以创建新表。
    * **从存储**或**从文件**中选择**引入类型** > 。
        * 如果选择了 "**从存储**"，请输入 "**链接到存储**" 以将 url 添加到存储中。 将[BLOB SAS URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)用于专用存储帐户。 
        * 如果选择了 "**从文件**"，请选择 "**浏览**"，然后将文件拖动到框中。
    * 选择 "**编辑架构**" 以查看和编辑表列配置。
 
    ![一键式引入源详细信息](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > 如果在*表*行中选择 "引入**新数据（预览）** "，则所选表名称将出现在**项目详细信息**中。

1. 如果选择了一个现有表，则将打开 "**映射列**" 窗口，以便将源数据列映射到目标表列。 
    * 使用**省略列**从表中删除目标列。 
    * 使用 "**新建列**" 将新列添加到表中。 

    ![映射列窗口](media/ingest-data-one-click/one-click-map-columns-window.png)

1. 在 "**架构**" 选项卡中：

    * 从下拉 >**未压缩**或**GZip**选择**压缩类型**。
    * 从下拉 > **JSON**、 **CSV**、 **TSV**、 **SCSV**、 **SOHSV**、 **TSVE**或**PSV**中选择**数据格式**。 
        * 选择**json**格式时，请选择 " **json 级别**： 1-10"。 级别会影响表列数据的描述。 
        * 如果选择 JSON 以外的格式：选中复选框将**包含列名称**，以忽略文件的标题行。    
    * **映射名称**是自动设置的，但可以编辑。
    * 如果选择了现有的表，则可以选择 "**映射列**" 按钮，以打开 "**映射列**" 窗口。

    ![一键式引入 csv 格式的架构 .png](media/ingest-data-one-click/one-click-csv-format.png)

1. 在**编辑器**中，选择右侧的 " **V** " 打开编辑器。 在编辑器中，您可以查看和复制从输入生成的自动查询。 

1.  在表中： 
    * 右键单击新的列标题以**更改数据类型**、**重命名列**、**删除列**、按**升序排序**或按**降序排序**。 在现有列上，只能使用数据排序。 
    * 双击要编辑的新列名称。

1. 选择 "**开始引入**" 创建表、创建映射和数据引入。

    ![一键式引入 json 格式架构](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>查询数据

1. 如果数据引入成功完成，则在 "**数据引入完成**" 窗口中，所有三个步骤都将标有绿色复选标记。 
 
    ![一次单击数据引入完成](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. 选择 " **V** " 打开查询。 复制到 Web UI 以编辑查询。

1. 右侧菜单包含**快速查询**和**工具**。 

    * **快速查询**包括指向包含示例查询的 Web UI 的链接。
    * **工具**包含指向 Web UI 的链接，其中包含用于通过运行相关 `.drop` 命令对问题进行故障排除的**Drop 命令**。

    > [!TIP]
    > 使用 `.drop` 命令可能会丢失数据。 请谨慎使用。

## <a name="next-steps"></a>后续步骤

* [在 Azure 中查询数据数据资源管理器 Web UI](web-query-data.md)
* [使用 Kusto 查询语言编写 Azure 数据资源管理器查询](write-queries.md)

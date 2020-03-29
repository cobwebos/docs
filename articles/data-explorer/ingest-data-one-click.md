---
title: 使用一键式引入将数据引入 Azure 数据资源管理器
description: 了解如何使用一键式引入将数据引入（载入）Azure 数据资源管理器。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 4a53f7e68501ce7f9b19dea0822d3896ec241fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444554"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>使用一键式引入将数据引入 Azure 数据资源管理器

本文介绍如何使用一键式引入快速引入采用 JSON 或 CSV 格式的新表。 可将数据从存储或本地文件引入到现有表或新表中。 使用直观的一键式向导，几分钟内即可引入数据。 然后，可以使用 Azure 数据资源管理器 Web UI 编辑表并运行查询。

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* 登录到[应用程序](https://dataexplorer.azure.com/)。
* [创建 Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)。
* 登录到 Web [UI](https://dataexplorer.azure.com/)并[添加到群集的连接](/azure/data-explorer/web-query-data#add-clusters)。

## <a name="ingest-new-data"></a>引入新数据

1. 在 Web UI 的左侧菜单中，右键单击“数据库”或“表”行，然后选择“引入新数据(预览版)”********。

    ![在 Web UI 中选择“一键式引入”](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. 在“引入新数据(预览版)”窗口中，选择“源”选项卡，并填写“项目详细信息”************：

    * 对于“表”，请从下拉列表中选择现有的表名称，或选择“新建”以创建新表********。
    * 对于“引入类型”，请选择“从存储”或“从文件”************。
      * 如果选择了“从存储”，请选择“存储的链接”，以添加 URL********。 对专用存储帐户使用 [Blob SAS URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)。 
      * 如果选择了“从文件”，请选择“浏览”并将相应的文件拖到框中。********
    * 选择“编辑架构”以查看并编辑表列配置。****
 
    ![一键式引入源详细信息](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > 如果针对某个表行中选择了“引入新数据(预览版)”，选定的表名称将显示在“项目详细信息”中。**********

1. 如果选择了现有的表，将打开“映射列”窗口，可在其中将源数据列映射到目标表列。**** 
    * 使用“省略列”可从表中删除目标列。****
    * 使用“新建列”可将新列添加到表中****。

    ![“映射列”窗口](media/ingest-data-one-click/one-click-map-columns-window.png)

1. 在“架构”选项卡中****：

    * 从下拉菜单中选择“压缩类型”，然后选择“无压缩”或“GZip”************。
    * 从下拉菜单中选择“数据格式”，然后选择“JSON”、“CSV”、“TSV”、“SCSV”、“SOHSV”、“TSVE”或“PSV”********************************。 
        * 选择“JSON”格式时，还必须选择“JSON 级别”（1 到 10）********。 级别会影响表列数据的描述。 
        * 如果选择非 JSON 的格式，则必须选中“包括列名”复选框以忽略文件的标题行****。
    * “映射名称”会自动设置，但可对其进行编辑。****
    * 如果选择了现有的表，则可以选择“映射列”以打开“映射列”窗口********。

    ![一键式引入 CSV 格式架构](media/ingest-data-one-click/one-click-csv-format.png)

1. 在“编辑器”窗格上方，选择“v”按钮以打开编辑器********。 在编辑器中，可以查看和复制基于输入生成的自动查询。 

1. 在表中： 
    * 右键单击新的列标题，并选择“更改数据类型”、“重命名列”、“删除列”、“升序排序”或“降序排序”********************。 对于现有的列，只能执行数据排序。 
    * 双击新列名称进行编辑。

1. 选择“开始引入”以创建表和映射，并开始数据引入****。

    ![一键式引入 JSON 格式架构](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>查询数据

1. 在“数据引入已完成”窗口中，如果数据引入已成功完成，则所有三个步骤都会标有绿色的勾选标记****。
 
    ![一键式数据引入完成](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. 选择“v”按钮，打开查询****。 复制到 Web UI 以编辑查询。

1. 右侧菜单包含“快速查询”和“工具”选项********。 

    * “快速查询”包含 Web UI 的链接以及示例查询****。
    * “工具”包括 Web UI 上的“删除命令”链接，借助此链接，可通过运行相关的 `.drop` 命令来排查问题********。

    > [!TIP]
    > 使用 `.drop` 命令时，可能会丢失数据。 因此请慎用。

## <a name="next-steps"></a>后续步骤

* [在 Azure 数据资源管理器 Web UI 中查询数据](web-query-data.md)
* [使用 Kusto 查询语言编写针对 Azure 数据资源管理器的查询](write-queries.md)

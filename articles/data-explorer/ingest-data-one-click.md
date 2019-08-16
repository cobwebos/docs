---
title: 使用一键式引入将数据引入 Azure 数据资源管理器
description: 了解如何只需使用一键式引入即可将数据引入 (加载) 到 Azure 数据资源管理器。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: b1ce2d9efe44021b4e3191739bd2f922e34c44cb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520044"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>使用一键式引入将数据引入 Azure 数据资源管理器

本文介绍如何使用一键式引入快速将新表从存储的 json 或 csv 格式引入到 Azure 数据资源管理器中。 引入数据后, 可以使用 Web UI 编辑表并运行查询。

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* 登录到[应用程序](https://dataexplorer.azure.com/)。
* 创建[Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)
* Azure 存储中的数据源。

## <a name="ingest-new-data"></a>引入新数据

1. 右键单击*数据库名称*, 然后选择 "引入**新数据" (预览)**

    ![在 web UI 中选择一次单击引入](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. 在 "**数据引入 (预览)** " 窗口的 "**源**" 选项卡中, 完成**项目详细信息**:

    * 输入新**表名**。 
    * 选择 "**从存储中** **引入类型** > "。
    * 输入指向**存储的链接**将 url 添加到存储。 将 Blob SAS URL 用于专用存储帐户。 
    * 选择**编辑架构**
 
    ![一键式引入源详细信息](media/ingest-data-one-click/one-click-ingestion-source.png) 

1. 在 "**架构**" 选项卡中, 从下拉 > **JSON**或**CSV**中选择 "**数据格式**"。 
   
   如果选择**CSV**:
    * 选中 "**忽略标题**" 复选框以忽略 csv 文件的标题行。    
    * **映射名称**是自动设置的, 但可以编辑。

    ![一键式引入 csv 格式的架构 .png](media/ingest-data-one-click/one-click-csv-format.png)

   如果选择**JSON**:
    * 选择**JSON 级别**:1-10 从下拉。 在右下角的表中显示 json 文件中的级别。 
    * **映射名称**是自动设置的, 但可以编辑。

    ![一键式引入 json 格式架构](media/ingest-data-one-click/one-click-json-format.png)  

1. 在**编辑器**中, 选择 " **V** on right" 打开编辑器。 在编辑器中, 您可以查看和复制从输入生成的自动查询。 

1.  在右下角的表中: 
    * 选择 "列右侧的**V** "**重命名列**、**删除列**、按**升序排序**或按**降序排序**
    * 双击列名称进行编辑。
    * 选择列名称左侧的图标以更改数据类型。 

1. 选择 "**开始引入**" 创建表、创建映射和数据引入。
 
## <a name="query-data"></a>查询数据

1. 如果数据引入成功完成, 则在 "**数据引入完成**" 窗口中, 所有三个步骤都将标有绿色复选标记。 
 
    ![一次单击数据引入完成](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. 选择 " **V** " 打开查询。 复制到 Web UI 以编辑查询。

1. 右侧菜单包含**快速查询**和**工具**。 

    * **快速查询**包括指向包含示例查询的 Web UI 的链接。
    * **工具**包含指向 Web UI 的链接, 其中包含用于通过运行相关`.drop`命令对问题进行故障排除的**Drop 命令**。

    > [!TIP]
    > 使用`.drop`命令可能会丢失数据。 请谨慎使用。

## <a name="next-steps"></a>后续步骤

* [在 Azure 中查询数据数据资源管理器 Web UI](web-query-data.md)
* [使用 Kusto 查询语言编写 Azure 数据资源管理器查询](write-queries.md)

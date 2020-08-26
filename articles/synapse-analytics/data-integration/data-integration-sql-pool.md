---
title: 将数据引入 SQL 池
description: 了解如何在 Azure Synapse 分析中将数据引入 SQL 池
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 63e83e69e5e09c17b2a2ddb5ca7bee6474e2fddd
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386667"
---
# <a name="ingest-data-into-a-sql-pool"></a>将数据引入 SQL 池

本文介绍如何使用 Azure Synapse Analytics 将数据从 Azure Data Lake 第2代存储帐户引入到 SQL 池中。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**：使用 Azure Data Lake Storage Gen 2 作为*源*数据存储。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)来了解创建步骤。
* **Azure Synapse 分析**：使用 SQL 池作为*接收器*数据存储。 如果没有 Azure Synapse Analytics 实例，请参阅[创建 SQL 池](../../azure-sql/database/single-database-create-quickstart.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)以获取创建一个的步骤。

## <a name="create-linked-services"></a>创建链接服务

在 Azure Synapse Analytics 中，链接服务是定义到其他服务的连接信息的一个位置。 在本部分中，你将添加 Azure Synapse 分析和 Azure Data Lake Storage Gen2 链接服务。

1. 打开 Azure Synapse Analytics UX，转到“管理”选项卡。
1. 在“外部连接”下，选择“链接服务”。
1. 若要添加链接服务，请单击“新建”。
1. 从列表中选择“Azure Data Lake Storage Gen2”磁贴，然后单击“继续”。
1. 输入你的身份验证凭据。 帐户密钥、服务主体和托管标识是目前支持的身份验证类型。 单击“测试连接”以验证你的凭据是否正确。 完成后，单击“创建”。
1. 重复步骤3-5，但不要 Azure Data Lake Storage Gen2，选择 "Azure Synapse Analytics" 磁贴，并在相应的连接凭据中输入。 对于 Azure Synapse 分析，当前支持 SQL 身份验证、托管标识和服务主体。

## <a name="create-pipeline"></a>创建管道

管道包含用于执行一组活动的逻辑流。 在本部分中，你将创建一个包含复制活动的管道，该活动将 ADLS Gen2 中的数据引入到 SQL 池中。

1. 转到“协调”选项卡。单击管道标题旁边的加号图标，然后选择“管道”。
1. 在“活动”窗格中的“移动和转换”下，将“复制数据”拖到管道画布上。
1. 单击复制活动并转到“源”选项卡。单击“新建”以创建新的源数据集。
1. 选择 Azure Data Lake Storage Gen2 作为数据存储，然后单击“继续”。
1. 选择 DelimitedText 作为格式，然后单击“继续”。
1. 在“设置属性”窗格中，选择你创建的 ADLS 链接服务。 指定源数据的文件路径，并指定第一行是否具有标题。 你可以从文件存储或示例文件导入架构。 完成后，单击“确定”。
1. 转到“接收器”选项卡。单击“新建”以创建新的接收器数据集。
1. 选择 "Azure Synapse Analytics" 作为数据存储，然后单击 "继续"。
1. 在 "设置属性" 窗格中，选择你创建的 Azure Synapse Analytics 链接服务。 如果要写入现有表，请从下拉列表中选择它。 否则，请选中 "**编辑**"，然后输入新的表名称。 完成后，单击“确定”
1. 如果要创建表，请在 "表选项" 字段中启用**自动创建表**。

## <a name="debug-and-publish-pipeline"></a>调试和发布管道

完成管道配置后，在发布项目之前你可以执行调试运行，以验证是否所有事项都正确。

1. 若要调试管道，请在工具栏上选择“调试”。 可以在窗口底部的“输出”选项卡中看到管道运行的状态。 
1. 在管道可以成功运行后，在顶部工具栏中选择“全部发布”。 此操作将你创建的实体（数据集和管道）发布到 Synapse Analytics 服务。
1. 等待“已成功发布”消息出现。 若要查看通知消息，请单击右上角的铃铛按钮。 


## <a name="trigger-and-monitor-the-pipeline"></a>触发和监视管道

在此步骤中，你将手动触发在上一步骤中发布的管道。 

1. 选择工具栏中的“添加触发器”，然后选择“立即触发”。  在“管道运行”页上选择“完成”。   
1. 转到左侧边栏中的“监视器”选项卡。 此时会看到由手动触发器触发的管道运行。 可以使用“操作”列中的链接来查看活动详细信息以及重新运行该管道。
1. 若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”链接。  此示例中只有一个活动，因此列表中只看到一个条目。 有关复制操作的详细信息，请选择“操作”列中的“详细信息”链接（眼镜图标）。  若要回到“管道运行”视图，请选择顶部的“管道运行”。 若要刷新视图，请选择“刷新”。
1. 验证数据是否已正确写入到 SQL 池中。


## <a name="next-steps"></a>后续步骤

有关 Synapse Analytics 的数据集成的详细信息，请参阅[引入 data into Azure Data Lake Storage Gen2](data-integration-data-lake.md)一文。

---
title: 在 Azure 突触分析中引入到 SQL 池
description: 了解如何在 Azure 同步分析中将数据引入 SQL 池
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: fbd8e03b1f8af7802133c35ae4860116aaea0c3c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430560"
---
# <a name="ingesting-data-into-a-sql-pool"></a>将数据引入 SQL 池

在本文中，您将了解如何使用 Azure 突触分析将 Azure 数据湖第 2 代存储帐户中的数据引入到 SQL 池中。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果没有 Azure 订阅，请先创建一个[免费的 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**：使用 Azure 数据存储第 2 代作为*源*数据存储。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)以执行创建存储帐户的步骤。
* **Azure 突触分析**：使用 SQL 池作为*接收器*数据存储。 如果没有 Azure 同步分析实例，请参阅[创建 SQL 池](../../sql-database/sql-database-get-started-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)以执行创建一个示例。

## <a name="create-linked-services"></a>创建链接服务

在 Azure Synapse 分析中，链接服务是定义与其他服务的连接信息的位置。 在本节中，您将添加 Azure 突触分析和 Azure 数据存储源第 2 代链接服务。

1. 打开 Azure 同步分析 UX 并转到 **"管理**"选项卡。
1. 在 **"外部连接**"下，选择 **"链接的服务**"。
1. 要添加链接的服务，请单击"**新建**"。
1. 从列表中选择 Azure 数据存储湖存储 Gen2 磁贴，然后单击"**继续**"。
1. 输入身份验证凭据。 帐户密钥、服务主体和托管标识当前受支持身份验证类型。 单击测试连接以验证您的凭据是否正确。 完成后，单击“创建”。****
1. 重复步骤 3-5，但选择 Azure 同步分析磁贴并输入相应的连接凭据，而不是 Azure 数据湖存储 Gen2。 对于 Azure 突触分析，当前支持 SQL 身份验证、托管标识和服务主体。

## <a name="create-pipeline"></a>创建管道

管道包含执行一组活动的逻辑流。 在本节中，您将创建一个管道，其中包含将 ADLS 第 2 代的数据引入 SQL 池的副本活动。

1. 转到 **"协调"** 选项卡。单击管道标题旁边的加号图标，然后选择 **"管道**"。
1. 在活动窗格中的 **"移动和转换"** 下，将 **"复制数据"** 拖动到管道画布上。
1. 单击复制活动并转到 **"源"** 选项卡。单击 **"新建"** 以创建新的源数据集。
1. 选择 Azure 数据存储第 2 代作为数据存储，然后单击"继续"。
1. 选择"分隔文本"作为格式，然后单击"继续"。
1. 在"设置属性"窗格中，选择您创建的 ADLS 链接服务。 指定源数据的文件路径，并指定第一行是否具有标头。 可以从文件存储或示例文件导入架构。 完成后，单击“确定”。
1. 转到 **"接收器"** 选项卡。单击 **"新建**"以创建新的接收器数据集。
1. 选择 Azure 同步分析作为数据存储，然后单击"继续"。
1. 在"设置属性"窗格中，选择您创建的 Azure 同步分析链接服务。 如果要写入现有表，请从下拉列表中选择它。 否则，选中 **"编辑"** 并输入新表名称。 完成后，单击“确定”
1. 如果要创建表，请在表选项字段中启用**自动创建表**。

## <a name="debug-and-publish-pipeline"></a>调试和发布管道

完成管道配置后，可以在发布项目之前执行调试运行，以验证一切是否正确。

1. 若要调试管道，请在工具栏上选择“调试”。**** 可以在窗口底部的“输出”选项卡中看到管道运行的状态。**** 
1. 在管道可以成功运行后，在顶部工具栏中选择“全部发布”。**** 此操作将发布您创建的到 Synapse 分析服务的实体（数据集和管道）。
1. 等待“已成功发布”消息出现。**** 要查看通知消息，请单击右上角的铃声按钮。 


## <a name="trigger-and-monitor-the-pipeline"></a>触发并监视管道

在此步骤中，您可以手动触发上一步骤中发布的管道。 

1. 选择工具栏中的“添加触发器”，然后选择“立即触发”。******** 在“管道运行”页上选择“完成”。********  
1. 转到左侧侧边栏中的 **"监视器"** 选项卡。 此时会看到由手动触发器触发的管道运行。 可以使用“操作”列中的链接来查看活动详细信息以及重新运行该管道。****
1. 若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”链接。******** 此示例中只有一个活动，因此列表中只看到一个条目。 有关复制操作的详细信息，请选择“操作”列中的“详细信息”链接（眼镜图标）。******** 若要回到“管道运行”视图，请选择顶部的“管道运行”****。 若要刷新视图，请选择“刷新”。****
1. 验证数据是否正确写入 SQL 池。


## <a name="next-steps"></a>后续步骤

有关 Synapse 分析的数据集成的详细信息，请参阅将数据[引入 Azure 数据存储第 2 代](data-integration-data-lake.md)。

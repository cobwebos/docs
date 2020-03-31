---
title: 使用流分析处理来自事件中心 Azure 的数据 |微软文档
description: 本文介绍如何使用 Azure 流分析作业处理 Azure 事件中心的数据。
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 531426656fe833752c9c4685688c00de3894895b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "69991951"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>使用 Azure 流分析处理事件中心的数据 
Azure 流分析服务使从 Azure 事件中心引入、处理和分析流数据变得容易，从而提供了强大的见解，从而推动实时操作。 通过此集成，您可以快速创建热路径分析管道。 可以使用 Azure 门户可视化传入数据并编写流分析查询。 查询准备就绪后，只需单击几下即可将其移动到生产中。 

## <a name="key-benefits"></a>主要优点
以下是 Azure 事件中心和 Azure 流分析集成的主要优势： 
- **预览数据**– 可以从 Azure 门户中的事件中心预览传入数据。
- **测试查询**– 准备转换查询并直接在 Azure 门户中进行测试。 有关查询语言语法，请参阅[流分析查询语言](/stream-analytics-query/built-in-functions-azure-stream-analytics)文档。
- **将查询部署到生产**中 – 您可以通过创建和启动 Azure 流分析作业将查询部署到生产中。

## <a name="end-to-end-flow"></a>端到端流

1. 登录到 Azure[门户](https://portal.azure.com)。 
1. 导航到**事件中心命名空间**，然后导航到具有传入数据**的事件中心**。 
1. 在事件中心页上选择 **"处理数据**"。  

    ![处理数据磁贴](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. 在"**从事件磁贴启用实时见解**"上选择 **"浏览**"。 

    ![选择流分析](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. 您将看到一个查询页，该页的值已为以下字段设置：
    1. 事件**中心**作为查询的输入。
    1. 使用 SELECT 语句**对 SQL 查询**进行示例。 
    1. 用于引用查询测试结果的**输出**别名。 

        ![查询编辑器](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  首次使用此功能时，此页面会请求您创建使用者组的权限以及活动中心预览传入数据的策略。
1. 在 **"输入预览**窗格中**创建**"，如上图所示。 
1. 您将立即在此选项卡中看到最新传入数据的快照。
    - 将自动检测数据中的序列化类型 （JSON/CSV）。 您也可以手动将其更改为 JSON/CSV/AVRO。
    - 您可以预览表格式或原始格式的传入数据。 
    - 如果显示的数据不是最新的，请选择“刷新”查看最新事件。**** 

        下面是**表格式**的数据示例：![表格式的结果](./media/process-data-azure-stream-analytics/snapshot-results.png)

        下面是**原始格式**数据的示例： 

        ![以原始格式的结果](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. 选择 **"测试"查询**，在 **"测试结果"** 选项卡中查看查询的测试结果快照。您还可以下载结果。

    ![测试查询结果](./media/process-data-azure-stream-analytics/test-results.png)
1. 编写您自己的查询以转换数据。 请参阅[流分析查询语言参考](/stream-analytics-query/stream-analytics-query-language-reference)。
1. 测试查询并希望将其移动到生产中后，选择 **"部署查询**"。 要部署查询，请创建 Azure 流分析作业，您可以在其中为作业设置输出，然后启动作业。 要创建流分析作业，请为作业指定名称，然后选择"**创建**"。

      ![创建 Azure 流分析作业](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  我们建议您为从事件中心页创建的每个新的 Azure 流分析作业创建使用者组和策略。 使用者组仅允许五个并发读取器，因此为每个作业提供专用使用者组将避免因超出该限制而可能出现的任何错误。 专用策略允许您轮换密钥或撤消权限，而不会影响其他资源。 
1. 现在，将创建流分析作业，其中查询与测试的查询相同，并且输入是事件中心。 

9.  要完成管道，请设置查询的**输出**，然后选择 **"开始"** 以启动作业。

    > [!NOTE]
    > 在开始作业之前，不要忘记将输出别名替换为在 Azure 流分析中创建的输出名称。

      ![设置输出并启动作业](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>已知的限制
测试查询时，测试结果大约需要 6 秒才能加载。 我们正在努力提高测试性能。 但是，在生产中部署时，Azure 流分析将具有低于秒的延迟。

## <a name="streaming-units"></a>流式处理单位
Azure 流分析作业默认为三个流式处理单元 （SUs）。 要调整此设置，请在 Azure 门户中的 **"流分析"作业**页中选择左侧菜单的 **"缩放**"。 要了解有关流式处理单元的更多，请参阅[了解和调整流式处理单元](../stream-analytics/stream-analytics-streaming-unit-consumption.md)。

![缩放流式处理单元](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>后续步骤
要了解有关流分析查询的更多信息，请参阅[流分析查询语言](/stream-analytics-query/built-in-functions-azure-stream-analytics)

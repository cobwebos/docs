---
title: 使用流分析处理来自事件中心 Azure 的数据 |Microsoft Docs
description: 本文介绍如何使用 Azure 流分析作业从 Azure 事件中心处理数据。
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 003e68b36ff71fb2991cf087ef33f72aba73a8be
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233958"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics-preview"></a>使用 Azure 流分析 (预览版) 从事件中心处理数据
利用 Azure 流分析服务, 可以轻松地从 Azure 事件中心引入、处理和分析流式处理数据, 实现功能强大的洞察力来驱动实时操作。 此集成使你可以快速创建热路径分析管道。 可以使用 Azure 门户直观显示传入数据并写入流分析查询。 查询准备就绪后, 只需单击几下鼠标就能将其移动到生产中。 

> [!NOTE]
> 此功能目前处于预览状态。 

## <a name="key-benefits"></a>主要优点
下面是 Azure 事件中心和 Azure 流分析集成的主要优点: 
- **预览数据**–可以在 Azure 门户中预览事件中心的传入数据。
- **测试查询**–准备转换查询并在 Azure 门户中直接对其进行测试。 有关查询语言语法, 请参阅[流分析查询语言](/stream-analytics-query/built-in-functions-azure-stream-analytics)文档。
- **将查询部署到生产**–可以通过创建和启动 Azure 流分析作业, 将查询部署到生产环境中。

## <a name="end-to-end-flow"></a>端到端流

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 导航到**事件中心命名空间**, 然后导航到包含传入数据的**事件中心**。 
1. 选择 "事件中心" 页上的 "**处理数据**"。  

    ![处理数据磁贴](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. 在 "**启用事件的实时见解**" 磁贴中选择 "**浏览**"。 

    ![选择流分析](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. 你会看到一个查询页面, 其中包含已为以下字段设置的值:
    1. 作为查询的输入的**事件中心**。
    1. 带有 SELECT 语句的示例**SQL 查询**。 
    1. 用于引用查询测试结果的**输出**别名。 

        ![查询编辑器](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  当你首次使用此功能时, 此页会要求你为你的事件中心创建使用者组和策略, 以便预览传入数据。
1. 在 "**输入预览**" 窗格中选择 "**创建**", 如上图所示。 
1. 你将立即在此选项卡中看到最新传入数据的快照。
    - 自动检测数据中的序列化类型 (JSON/CSV)。 还可以手动将其更改为 JSON/CSV/AVRO。
    - 可以预览表格式或原始格式的传入数据。 
    - 如果显示的数据不是最新的, 请选择 "**刷新**" 以查看最新的事件。 

        下面是**表格格式**的数据示例: ![表格式的结果](./media/process-data-azure-stream-analytics/snapshot-results.png)

        下面是**原始格式**的数据示例: 

        ![采用原始格式的结果](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. 选择 "**测试查询**", 以在 "**测试结果**" 选项卡中查看查询的测试结果的快照。你还可以下载结果。

    ![测试查询结果](./media/process-data-azure-stream-analytics/test-results.png)
1. 编写您自己的查询以转换数据。 请参阅[流分析查询语言参考](/stream-analytics-query/stream-analytics-query-language-reference)。
1. 测试查询并想要将其移入生产环境后, 请选择 "**部署查询**"。 若要部署该查询, 请创建一个 Azure 流分析作业, 你可以在其中设置作业的输出, 并启动作业。 若要创建流分析作业, 请指定该作业的名称, 然后选择 "**创建**"。

      ![创建 Azure 流分析作业](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  建议为你在 "事件中心" 页中创建的每个新的 Azure 流分析作业创建使用者组和策略。 使用者组仅允许5个并发读取者, 因此为每个作业提供专用使用者组将避免超出该限制可能产生的任何错误。 专用策略允许你轮换密钥或撤消权限, 而不会影响其他资源。 
1. 你的流分析作业现在会创建, 其中你的查询与你测试的相同, 并且输入是事件中心。 

9.  若要完成管道, 请设置查询的**输出**, 然后选择 "**启动**" 以启动作业。

    > [!NOTE]
    > 在启动作业之前, 不要忘记将 outputalias 替换为你在 Azure 流分析中创建的输出名称。

      ![设置输出并启动作业](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>已知限制
测试查询时, 需要大约6秒钟的时间来加载测试结果。 我们正在努力提高测试性能。 但是, 在生产环境中部署时, Azure 流分析会有亚秒级的延迟。

## <a name="streaming-units"></a>流式处理单位
Azure 流分析作业默认为三个流式处理单位 (SUs)。 若要调整此设置, 请在 Azure 门户的**流分析作业**页的左侧菜单中选择 "**缩放**"。 若要了解有关流式处理单元的详细信息, 请参阅[了解和调整流式处理单位](../stream-analytics/stream-analytics-streaming-unit-consumption.md)。

![缩放流式处理单元](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>后续步骤
若要了解有关流分析查询的详细信息, 请参阅[流分析查询语言](/stream-analytics-query/built-in-functions-azure-stream-analytics)

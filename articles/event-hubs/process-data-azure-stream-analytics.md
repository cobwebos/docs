---
title: 处理事件中心使用从 Azure Stream Analytics 数据 |Microsoft Docs
description: 本文介绍如何处理 Azure 事件中心使用 Azure Stream Analytics 作业的数据。
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: f179687b0983e145244e228a3d3b06b4eabead48
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723410"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>处理来自事件中心使用 Azure Stream Analytics 数据
Azure Stream Analytics 服务轻松引入、 处理和分析从 Azure 事件中心的流式处理数据启用强大促进实时操作见解。 此集成使你能够快速创建热路径分析管道。 可以使用 Azure 门户来可视化传入的数据和编写 Stream Analytics 查询。 查询准备就绪后，可以将其移到生产环境中只需单击几。 

## <a name="key-benefits"></a>主要优点
下面是 Azure 事件中心和 Azure Stream Analytics 集成的主要优点： 
- **预览数据**– 您可以预览从 Azure 门户中的事件中心的传入数据。
- **测试查询**– 准备转换查询，并直接在 Azure 门户中进行测试。 查询语言语法，请参阅[Stream Analytics 查询语言](/stream-analytics-query/built-in-functions-azure-stream-analytics)文档。
- **部署到生产环境查询**– 你可以通过创建和启动 Azure Stream Analytics 作业部署到生产查询。

## <a name="end-to-end-flow"></a>端到端流

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 导航到您**事件中心命名空间**，然后导航到**事件中心**，其中包含传入的数据。 
1. 选择**处理数据**事件中心页上。  

    ![进程数据磁贴](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. 选择**浏览**上**启用实时地从事件**磁贴。 

    ![选择流分析](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. 你看到查询页的值已设置为以下字段：
    1. 你**事件中心**作为查询的输入。
    1. 示例**SQL 查询**使用 SELECT 语句。 
    1. **输出**别名以引用你的测试结果。 

        ![查询编辑器](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  当首次使用此功能时，此页会询问你创建使用者组，以及为事件中心策略预览传入数据的权限。
1. 选择**创建**中**输入预览**窗格中，在上图中所示。 
1. 您可以立即查看此选项卡中最新的传入数据的快照。
    - 你的数据中的序列化类型会自动检测到 (JSON/CSV)。 您可以手动将其更改也为 JSON/CSV/AVRO。
    - 您可以预览中的表格格式或原始格式的传入数据。 
    - 如果显示的数据不是最新，则选择**刷新**若要查看最新事件。 

        下面是一个示例中的数据**表格式**: ![中的表格格式的结果](./media/process-data-azure-stream-analytics/snapshot-results.png)

        下面是一个示例中的数据**原始格式**: 

        ![以原始格式的结果](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. 选择**测试查询**若要查看测试结果的查询中的快照**测试结果**选项卡。此外可以下载结果。

    ![测试查询结果](./media/process-data-azure-stream-analytics/test-results.png)
1. 编写您自己的查询来转换数据。 请参阅[Stream Analytics 查询语言参考](/stream-analytics-query/stream-analytics-query-language-reference)。
1. 一旦你已在测试查询，且你想要将其移到生产环境中，选择**部署查询**。 若要部署该查询，创建 Azure Stream Analytics 作业，可以在此作业，设置一个输出，并启动作业。 若要创建 Stream Analytics 作业，请指定作业的名称，然后选择**创建**。

      ![创建 Azure 流分析作业](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  我们建议创建一个使用者组并为每个新的 Azure Stream Analytics 作业从事件中心页创建的策略。 使用者组，这样只有五个并发读取者，为每个作业提供专用使用者组会避免超出该限制从可能引发的任何错误。 专用的策略，可将密钥轮换或撤消权限而不会影响其他资源。 
1. Stream Analytics 作业现已创建的查询是相同的测试，并输入是事件中心。 

9.  若要完成管道，设置**输出**查询，然后选择**启动**启动作业。

    > [!NOTE]
    > 在开始之前该作业，别忘了将在 Azure Stream Analytics 中创建的输出名称替换为输出别名。

      ![设置输出并启动作业](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>已知限制
测试您的查询，时测试结果需要大约 6 秒钟才能加载。 我们正在努力提高性能的测试。 但是，在生产中部署，Azure Stream Analytics 将具有次秒级滞后时间。

## <a name="streaming-units"></a>流式处理单位
Azure Stream Analytics 作业默认值为三个流式处理单位 (Su)。 若要调整此设置，请选择**规模**中的左侧菜单上**Stream Analytics 作业**在 Azure 门户中的页。 若要了解有关流式处理单位的详细信息，请参阅[了解和调整流单元](../stream-analytics/stream-analytics-streaming-unit-consumption.md)。

![缩放流式处理单元](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>后续步骤
若要了解有关 Stream Analytics 查询的详细信息，请参阅[Stream Analytics 查询语言](/stream-analytics-query/built-in-functions-azure-stream-analytics)
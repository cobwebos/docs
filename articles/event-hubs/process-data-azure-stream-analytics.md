---
title: 使用 Azure 流分析处理事件中心的数据 | Microsoft Docs
description: 本文介绍如何使用 Azure 流分析作业处理 Azure 事件中心的数据。
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 2db4fba59a1a06a24ee2939c51ecdf65aa06cef3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85312888"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>使用 Azure 流分析处理事件中心的数据 
利用 Azure 流分析服务，可以轻松地通过 Azure 事件中心引入、处理和分析流数据，获得进行实时操作所需的强大洞察力。 此集成使你可以快速创建热路径分析管道。 可以通过 Azure 门户直观显示传入数据以及编写流分析查询。 查询准备就绪后，单击几下鼠标即可将其移到生产环境中。 

## <a name="key-benefits"></a>主要优点
下面是 Azure 事件中心和 Azure 流分析集成的主要优点： 
- **预览数据** – 可以在 Azure 门户中预览事件中心的传入数据。
- **测试查询** – 准备一个转换查询并直接在 Azure 门户中对其进行测试。 有关查询语言语法，请参阅[流分析查询语言](/stream-analytics-query/built-in-functions-azure-stream-analytics)文档。
- **将查询部署到生产** – 可以通过创建并启动 Azure 流分析作业，将查询部署到生产环境中。

## <a name="end-to-end-flow"></a>端到端流

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 导航到你的**事件中心命名空间**，然后导航到包含传入数据的**事件中心**。 
1. 在事件中心页上选择“处理数据”。  

    ![“处理数据”磁贴](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. 在“启用根据事件得出实时见解”磁贴上选择“浏览”。  

    ![选择流分析](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. 你会看到一个查询页面，其中包含已为以下字段设置的值：
    1. 你的**事件中心**，用作查询的输入。
    1. 包含 SELECT 语句的示例 **SQL 查询**。 
    1. 一个**输出**别名，用于引用查询测试结果。 

        ![查询编辑器](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  当你首次使用此功能时，此页会要求你授予相关权限，以便为你的事件中心创建使用者组和策略，然后你就可以预览传入数据。
1. 在“输入预览”窗格中选择“创建”，如上图所示。  
1. 你会立即在此选项卡中看到最新传入数据的快照。
    - 系统会自动检测数据中的序列化类型 (JSON/CSV)。 还可以手动将序列化类型更改为 JSON/CSV/AVRO。
    - 可以按表格式或原始格式预览传入数据。 
    - 如果显示的数据不是最新的，请选择“刷新”查看最新事件。 

        下面是表格式数据示例： ![表格式结果](./media/process-data-azure-stream-analytics/snapshot-results.png)

        下面是原始格式数据示例： 

        ![原始格式结果](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. 选择“测试查询”即可在“测试结果”选项卡中查看查询测试结果的快照。 还可以下载结果。

    ![测试查询结果](./media/process-data-azure-stream-analytics/test-results.png)
1. 编写你自己的查询来转换数据。 请参阅 [Stream Analytics Query Language reference](/stream-analytics-query/stream-analytics-query-language-reference)（流分析查询语言参考）。
1. 测试查询后，如果想要将其移入生产环境，请选择“部署查询”。 若要部署查询，请创建一个 Azure 流分析作业，以便在其中设置作业的输出，然后启动作业。 若要创建流分析作业，请指定作业的名称，然后选择“创建”。

      ![创建 Azure 流分析作业](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  建议你为在“事件中心”页创建的每个新的 Azure 流分析作业创建一个使用者组和一个策略。 使用者组仅允许 5 个并发读者。因此，如果为每个作业提供一个专用使用者组，即可避免超出该限制后可能会产生的任何错误。 专用策略允许你轮换密钥或撤销权限而不影响其他资源。 
1. 你的流分析作业现已创建，其中，你的查询与测试的相同，输入是你的事件中心。 

9.  若要完成管道，请设置查询的“输出”，然后选择“启动”以启动作业。 

    > [!NOTE]
    > 在启动作业之前，请勿忘记将 outputalias 替换为你在 Azure 流分析中创建的输出名称。

      ![设置输出并启动作业](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>已知的限制
测试查询时，需要大约 6 秒钟的时间来加载测试结果。 我们正努力提高测试性能。 但是，部署到生产环境中以后，Azure 流分析的延迟将是亚秒级的。

## <a name="streaming-units"></a>流式处理单位
Azure 流分析作业默认设置为三个流单元 (SU)。 若要调整此设置，请在 Azure 门户中的“流分析作业”页的左侧菜单中选择“缩放”。 若要详细了解流单元，请参阅[了解和调整流单元](../stream-analytics/stream-analytics-streaming-unit-consumption.md)。

![缩放流单元](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>后续步骤
若要详细了解流分析查询，请参阅[流分析查询语言](/stream-analytics-query/built-in-functions-azure-stream-analytics)

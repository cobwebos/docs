---
title: Azure 流分析故障排除指南
description: 本文介绍对 Azure 流分析作业、连接、输入、输出、查询和数据进行故障排除的技巧。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 44777946fdc829da222ffdd67dfecfa3bf240be7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Azure 流分析故障排除指南

Azure 流分析故障排除可能看上去是一项复杂的工作。 经过我们与大量用户的合作后，我们创建了此指南以帮助简化此流程并避免用户对输入、输出、查询和函数的猜想。

## <a name="troubleshoot-your-stream-analytics-job"></a>对流分析作业进行故障排除

为了使流分析作业故障排除获得最佳结果，请按照以下指导进行操作：

1.  测试连接：
    - 使用每项输入和输出对应的“测试连接”按钮来验证与输入和输出的连接。

2.  检查输入数据：
    - 若要验证输入数据是否流入事件中心，请使用[服务总线资源管理器](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a)连接到 Azure 事件中心（如果使用了事件中心输入）。  
    - 使用每项输入对应的[“示例数据”](stream-analytics-sample-data-input.md)按钮，并下载输入示例数据。
    - 检查示例数据，以了解数据的形式：架构和[数据类型](https://msdn.microsoft.com/library/azure/dn835065.aspx)。

3.  测试查询：
    - 在“查询”选项卡中，使用“测试”按钮测试查询，并使用下载的示例数据[测试查询](stream-analytics-test-query.md)。 检查并尝试修正所有错误。
    - 如果使用了 [Timestamp By](https://msdn.microsoft.com/library/azure/mt573293.aspx)，请验证事件的时间戳是否大于[作业开始时间](stream-analytics-out-of-order-and-late-events.md)。

4.  调试查询：
    - 使用步骤将查询从简单的 Select 语句逐渐重新生成为更复杂的聚合。 使用 [WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx) 子句逐步生成查询逻辑。
    - 使用 [SELECT INTO](stream-analytics-select-into.md) 调试查询步骤。

5.  避免常犯的错误，例如：
    - 查询中的一个 [WHERE](https://msdn.microsoft.com/library/azure/dn835048.aspx) 子句筛选掉了所有事件，从而阻止生成输出。
    - 使用窗口函数时，请等待整个窗口持续时间完成，以查看查询中的输出。
    - 事件的时间戳要先于作业开始时间，因此事件会被删除。

6.  使用事件排序：
    - 如果之前的所有步骤都可以正常执行，请转到“设置”边栏选项卡并选择[“事件排序”](stream-analytics-out-of-order-and-late-events.md)。 验证此策略的配置是否适合方案。 使用“测试”按钮测试查询时，不会应用此策略。 这是在浏览器中测试与在生产中运行作业之间的一个差别。

7.  使用指标进行调试：
    - 如果在经过预期的持续时间（基于查询）后未获得任何输出，请尝试以下操作：
        - 在“监视器”选项卡上查看[“监控指标”](stream-analytics-monitoring.md)。由于值将进行聚合，因此指标会延迟几分钟。
            - 如果“输入事件数”大于 0，则作业可以读取输入数据。 如果“输入事件”不大于 0，则：
                - 若要查看数据源是否具有有效数据，请使用[服务总线资源管理器](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a)。 如果作业使用事件中心作为输入，则会应用此检查。
                - 检查以查看数据序列化格式和数据编码是否符合预期。
                - 如果该作业正在使用事件中心，请检查以查看消息正文是否为 Null。
            - 如果“数据转换错误数”大于 0 且在不断增加，则可能出现以下情况：
                - 作业可能无法反序列化事件。
                - 事件架构可能与查询中事件的已定义/预期架构不匹配。
                - 事件中某些字段的数据类型可能不符合预期。
            - 如果“运行时错误数”大于 0，则表示作业可以接收数据，但在处理查询时将遇到错误。
                - 若要查找错误，请转到[审核日志](../azure-resource-manager/resource-group-audit.md)并筛选“失败”状态。
            - 如果“输入事件数”大于 0 且“输出事件数”等于 0，则会出现以下情况之一：
                - 查询处理导致生成零个输出事件。
                - 事件或其字段可能出现格式错误，导致完成查询处理后生成零个输入。
                - 由于连接/身份验证的原因，作业无法将数据推送到[输出接收器](stream-analytics-select-into.md)。
        - 对于前文所述的所有这些错误，操作日志消息会解释其他详细信息（包括发生了什么情况），但如果查询逻辑筛选掉了所有事件，则不提供这些详细信息。 如果处理多个事件时出现错误，流分析会将 10 分钟内类型相同的前 3 个错误消息记录在操作日志中。 随后它将阻止其他的相同错误，并且显示一条“错误发生速度过快，已禁止显示此类错误”的消息。

8. 使用审核和诊断日志进行调试：
    - 使用[审核日志](../azure-resource-manager/resource-group-audit.md)并筛选以识别和调试错误。
    - 使用[作业诊断日志](stream-analytics-job-diagnostic-logs.md)识别和调试错误。

9. 检查输出：
    - 作业状态为“正在运行”时，经过查询中指定的持续时间后，可以在接收器数据源中看到输出。
    - 如果看不到即将转化为特定输出类型的输出，则将它们重定向到复杂程度更低的输出类型，如 Azure Blob。 可以使用存储资源管理器检查是否能看到输出。 此外，请验证对输出的限制是否会阻止接收数据。

10. 配合使用数据流分析和作业关系图指标：
    - 若要分析数据流和识别问题，请使用[带有指标的作业关系图](stream-analytics-job-diagram-with-metrics.md)。

11. 提交支持案例：
    - 最后，如果所有其他方法均失败，请使用包含作业的 SubscriptionID 提交 Microsoft 支持案例。

## <a name="get-help"></a>获取帮助

如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

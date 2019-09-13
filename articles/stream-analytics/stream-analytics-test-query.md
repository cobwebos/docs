---
title: 使用样本数据测试 Azure 流分析作业
description: 本文介绍如何使用 Azure 门户测试 Azure 流分析作业、示例输入以及上传样本数据。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/23/2019
ms.custom: seodec18
ms.openlocfilehash: 9c67d511f6c94c8b9af034835e149875304e2235
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918969"
---
# <a name="test-an-azure-stream-analytics-job-with-sample-data"></a>使用样本数据测试 Azure 流分析作业

在 Azure 流分析中，无需启动或停止作业即可测试查询。 可以从输入接收器测试传入数据的查询，或从 Azure 门户上的本地文件上传示例数据。 你还可以从本地示例数据或[Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-live-data-local-testing)中的实时数据或[Visual Studio Code](https://docs.microsoft.com/en-us/azure/stream-analytics/vscode-local-run)中本地测试查询。 

## <a name="sample-incoming-data-from-input"></a>来自输入的示例传入数据

Azure 流分析自动从流式处理输入中提取事件。 您可以对默认示例运行查询或设置该示例的特定时间范围。

1. 登录到 Azure 门户。

2. 找到并选择现有的流分析作业。

3. 在“流分析作业”页上的“作业拓扑”标题下，选择“查询”以打开“查询编辑器”窗口。 

4. 若要查看传入事件的示例列表，请选择 "使用文件输入" 图标，示例事件将自动显示在**输入预览**中。 

   a. 如果数据的 JSON 或 CSV，则会自动检测数据的序列化类型。 可以通过更改下拉菜单中的选项，手动将其更改为 JSON、CSV 和 AVRO。
    
   b. 使用选择器以**表**或**原始**格式查看数据。
    
   c. 如果显示的数据不是最新的, 请选择 "**刷新**" 以查看最新的事件。

   下表是**表格式**的数据示例：

   ![表格式的 Azure 流分析示例输入](./media/stream-analytics-test-query/asa-sample-table.png)

   下表是**原始格式**的数据示例：

   ![原始格式的 Azure 流分析示例输入](./media/stream-analytics-test-query/asa-sample-raw.png)

5. 若要测试传入数据的查询，请选择 "**测试查询**"。 结果将显示在 "**测试结果**" 选项卡中。你还可以选择 "**下载结果**" 以下载结果。

   ![Azure 流分析示例测试查询结果](./media/stream-analytics-test-query/asa-test-query.png)

6. 若要针对传入事件的特定时间范围测试查询，请选择 "**选择时间范围**"。
   
   ![传入示例事件的 Azure 流分析时间范围](./media/stream-analytics-test-query/asa-select-time-range.png)

7. 设置要用于测试查询的事件的时间范围，然后选择 "**示例**"。 在此时间范围内，最多可以检索1000个事件或 1 MB，以先达到的限制为准。

   ![Azure 流分析设置传入示例事件的时间范围](./media/stream-analytics-test-query/asa-set-time-range.png)

8. 对所选时间范围的事件进行采样后，它们将出现在 "**输入预览**" 选项卡中。

   ![Azure 流分析查看测试结果](./media/stream-analytics-test-query/asa-view-test-results.png)

9. 选择 "**重置**" 以查看传入事件的示例列表。 如果选择 "**重置**"，则时间范围选择将丢失。 选择 "**测试查询**" 以测试查询并在 "**测试结果**" 选项卡中查看结果。

10. 更改查询时，请选择 "**保存查询**" 以测试新的查询逻辑。 这允许你以迭代方式修改查询并再次测试，以查看输出如何变化。

11. 验证浏览器中显示的结果后，就可以**开始**作业了。

## <a name="upload-sample-data-from-a-local-file"></a>从本地文件上传示例数据

您可以使用本地文件中的示例数据来测试 Azure 流分析查询，而不是使用实时数据。

1. 登录到 Azure 门户。
   
2. 找到现有流分析作业并选择它。

3. 在“流分析作业”页上的“作业拓扑”标题下，选择“查询”以打开“查询编辑器”窗口。

4. 若要使用本地文件来测试查询，请在 "**输入预览**" 选项卡上选择 "上**传示例输入**"。 

   ![Azure 流分析上传示例文件](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. 上传本地文件以测试查询。 只能上载具有 JSON、CSV 或 AVRO 格式的文件。 选择“确定”。

   ![Azure 流分析上传示例文件](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. 上传文件后，还可以在窗体中以表或原始格式查看文件内容。 如果选择 "**重置**"，示例数据将返回到上一部分中所述的传入输入数据。 您可以随时上传任何其他文件来测试查询。

7. 选择 "**测试查询**"，针对上传的示例文件测试查询。

8. 测试结果将基于查询显示。 您可以更改查询，然后选择 "**保存查询**" 以测试新的查询逻辑。 这允许你以迭代方式修改查询并再次测试，以查看输出如何变化。

9. 在查询中使用多个输出时，将基于所选输出显示结果。 

   ![Azure 流分析选定的输出](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. 验证浏览器中显示的结果后，可以**启动**作业。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [常见流分析使用模式的查询示例](stream-analytics-stream-analytics-query-patterns.md)

* [了解 Azure 流分析的输入](stream-analytics-add-inputs.md)

* [了解 Azure 流分析的输出](stream-analytics-define-outputs.md)
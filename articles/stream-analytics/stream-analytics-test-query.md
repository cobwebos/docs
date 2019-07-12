---
title: 使用样本数据测试 Azure 流分析作业
description: 本文介绍如何使用 Azure 门户测试 Azure 流分析作业、示例输入以及上传样本数据。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2019
ms.custom: seodec18
ms.openlocfilehash: ad0e0ca75bf3d3a8d9d1029d42f8609b3c4c627b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620832"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>使用样本数据测试流分析查询

通过使用 Azure Stream Analytics，可以从输入数据采样或上传示例数据在 Azure 门户中测试查询，而无需启动或停止作业。

## <a name="upload-or-sample-data-from-a-live-source-to-test-the-query"></a>上传或从实时源测试查询的示例数据

1. 登录到 Azure 门户。 

2. 找到现有流分析作业并选择它。

3. 在“流分析作业”页上的“作业拓扑”  标题下，选择“查询”  以打开“查询编辑器”窗口。 

4. 若要测试你的查询可以既采样实时输入或从文件上的传的数据。 此数据必须以 JSON、CSV 或 AVRO 进行序列化。 示例输入必须以 UTF-8 进行编码，并且不能进行压缩。 仅支持使用逗号 (,) 分隔符来测试门户上的 CSV 输入。

    1. 使用实时输入： 右键单击任意输入。 然后选择**示例数据从输入**。 在下一屏幕中，可以设置示例的持续时间。 采样来自实时源的事件将检索最多 1000 个事件或 1 MB （具体取决于第一个），因此采样的数据可能无法表示指定的完全时间间隔。

    1. 使用文件： 右键单击任意输入。 并选择“上传文件中的样本数据”  。 

    ![流分析查询编辑器测试查询](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. 采样或上传完成后，选择**测试**要测试此查询针对具有提供的示例数据。

    ![流分析查询编辑器测试示例数据](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. 如果需要测试输出以供将来使用，浏览器中显示查询输出，以及用于下载结果的链接。 

7. 反复修改查询，并对其再次进行测试，以查看输出有何变化。

   ![流分析查询编辑器示例输出](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   在查询中使用多个输出时，结果将显示在单独的选项卡上，可以在它们之间轻松切换。

8. 验证浏览器中显示的结果后，**保存**查询。 然后**启动**作业，并让它处理传入事件。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

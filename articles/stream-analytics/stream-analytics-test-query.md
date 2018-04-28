---
title: 使用样本数据测试 Azure 流分析作业 | Microsoft Docs
description: 如何测试流分析作业中的查询。
keywords: 测试作业, 输入采样, 上传样本数据
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: stream-analytics
ms.workload: data-services
ms.date: 03/18/2018
ms.author: sngun
ms.openlocfilehash: c026a91fff5b8ef5774993b335f8d61877aa5d39
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="test-your-stream-analytics-query-with-sample-data"></a>使用样本数据测试流分析查询

使用 Azure 流分析，可以在门户中上传样本数据并测试查询，且不需要启动或停止作业。

## <a name="upload-sample-data-and-test-the-query"></a>上传样本数据并测试查询

1. 导航到某个现有的流分析作业 > 单击“查询”以打开查询编辑器窗口。 

2. 若要使用样本输入数据来测试查询，请右键单击任意输入，并选择“上载文件中的示例数据”。 当前只能上传 JSON 格式的数据。 如果数据采用其他格式（如 CSV），则应在上传前将其转换为 JSON 格式。 可以使用任何开源转换工具（如 [CSV 到 JSON 转换器](http://www.convertcsv.com/csv-to-json.htm)）将数据转换为 JSON 格式。

    ![流分析查询编辑器测试查询](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

3. 完成上传后，单击“测试”，以针对已提供的示例数据来测试此查询。

    ![流分析查询编辑器测试示例数据](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

4. 要保存测试输出以供将来使用，浏览器中显示查询输出，以及下载结果链接。 现在可以轻松反复地修改查询，并对其进行重复测试，以查看输出有何变化。

   ![流分析查询编辑器示例输出](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

当在查询中使用多个输出时，可以单独查看每个输出的结果，并在它们之间轻松切换。 在验证浏览器中显示的结果后，可以保存查询、启动作业并使其在不出现错误的情况下处理事件。
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

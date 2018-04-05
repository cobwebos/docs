---
title: 使用 SELECT INTO 调试 Azure 流分析查询 | Microsoft Docs
description: 在流分析中使用 SELECT INTO 语句进行数据中间查询的示例
keywords: ''
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: f232ba9804886f416ac0941ddc7e602782b6ebe2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="debug-queries-by-using-select-into-statements"></a>使用 SELECT INTO 语句调试查询

在实时数据处理中，掌握查询过程中数据的状态是十分有用的。 由于可以多次读取 Azure 流分析作业的输入或步骤，因此可以编写额外的 SELECT INTO 语句。 这样做会将中间数据输出至存储，并允许检查数据的正确性，就如调试程序时的监视变量一样。

## <a name="use-select-into-to-check-the-data-stream"></a>使用 SELECT INTO 检查数据流

下列 Azure 流分析作业中的示例查询具有一个流输入、两个引用数据输入和一个向 Azure 表存储的输出。 查询联接数据中心和两个引用 blob 中的数据，以获取名称和类别信息：

![示例 SELECT INTO 查询](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

请注意，虽然作业正在运行，但在输出中未生成任何事件。 在如下所示的“监视”磁贴上，可以看见输入正在生成数据，但你不知道 JOIN 的哪个步骤导致删除所有事件。

![“监视”磁贴](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
在此情况下，可添加几个额外的 SELECT INTO 语句，用于“记录”中间 JOIN 结果，以及从输入中读取的数据。

此示例中添加了两个新的“临时输出”。 可任意选择你喜欢的接收器。 此处使用 Azure 存储作为示例：

![添加额外的 SELECT INTO 语句](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

然后，可以重写查询，如下所示：

![重写的 SELECT INTO 查询](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

现在再次启动作业，并运行数分钟。 查询 temp1 和 temp2 通过 Visual Studio 云资源管理器生成下列各表：

**temp1 表**
![SELECT INTO temp1 表](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 表**
![SELECT INTO temp2 表](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

可以看到，temp1 和 temp2 都拥有数据，且 temp2 中正确填充了名称列。 但是，由于输出中没有数据，因此存在问题：

![SELECT INTO output1 表不包含数据](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

通过数据采样，几乎可以确定此问题与第二个 JOIN 有关。 可以从 blob 下载并查看引用数据：

![SELECT INTO ref 表](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

可以看到，此引用数据中的 GUID 的格式与 temp2 中 [来自] 列的格式不同。 这就是数据无法按预期到达 output1 的原因。

可以修复数据格式、将其上传至引用 blob，然后再重新尝试：

![SELECT INTO temp 表](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

此时，输出中的数据按预期格式化和填充。

![SELECT INTO final 表](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)


## <a name="get-help"></a>获取帮助

如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


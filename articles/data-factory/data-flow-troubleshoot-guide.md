---
title: 排除数据流故障
description: 了解如何在 Azure 数据工厂中解决数据流问题。
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: e2e1ddd031041f49107545cd0b3d3de4eaebcd6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472122"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>在 Azure 数据工厂中排除数据流故障

本文探讨 Azure 数据工厂中数据流的常见故障排除方法。

## <a name="common-errors-and-messages"></a>常见错误和消息

### <a name="error-code-df-executor-sourceinvalidpayload"></a>错误代码：DF-执行器-源无效负载
- **消息**： 数据预览、调试和管道数据流执行失败，因为容器不存在
- **原因**：当数据集包含存储中不存在的容器时
- **建议**：确保数据集中引用的容器存在或可访问。

### <a name="error-code-df-executor-systemimplicitcartesian"></a>错误代码：DF-执行器-系统隐式卡特

- **消息**： 不支持内部联接的隐式点菜产品，而是使用 CROSS JOIN。 联接中使用的列应为行创建唯一的键。
- **原因**：不支持逻辑计划之间用于 INNER 联接的隐式点菜产品。 如果联接中使用的列创建唯一键，则至少需要关系两侧的一列。
- **建议**：对于非相等的联接，您必须选择 CUSTOM CROSS JOIN。

### <a name="error-code-df-executor-systeminvalidjson"></a>错误代码：DF-执行器-系统无效

- **消息**： JSON 分析错误、不支持的编码或多行
- **原因**： JSON 文件可能存在问题：不支持的编码、损坏的字节或使用 JSON 源作为许多嵌套行上的单个文档
- **建议**：验证 JSON 文件的编码是否受支持。 在使用 JSON 数据集的源转换上，展开"JSON 设置"并打开"单一文档"。
 
### <a name="error-code-df-executor-broadcasttimeout"></a>错误代码：DF 执行器-广播超时

- **消息**： 广播联接超时错误，确保广播流在调试运行中生成数据 60 秒以内，在作业运行中生成 300 秒的数据
- **原因**：在调试运行中广播的默认超时为 60 秒，在作业运行中默认超时为 300 秒。 选择广播的流似乎很大，无法在此限制内生成数据。
- **建议**：避免广播处理可能需要 60 秒以上的大型数据流。 选择较小的流进行广播。 大型 SQL/DW 表和源文件通常是坏候选项。

### <a name="error-code-df-executor-conversion"></a>错误代码：DF-执行器转换

- **消息**：由于字符无效，转换为日期或时间失败
- **原因**： 数据未采用预期格式
- **建议**：使用正确的数据类型

### <a name="error-code-df-executor-invalidcolumn"></a>错误代码：DF-执行器-无效列

- **消息**：列名称需要在查询中指定，如果使用 SQL 函数设置别名
- **原因**：未指定列名称
- **建议**：如果使用 SQL 函数（如最小值/max（）等，则设置别名。

## <a name="general-troubleshooting-guidance"></a>常规故障排除指南

1. 检查数据集连接的状态。 在每个源和接收器转换中，访问您正在使用的每个数据集的链接服务并测试连接。
1. 从数据流设计器检查文件和表连接的状态。 打开调试并单击源转换上的数据预览，以确保您能够访问数据。
1. 如果数据预览中的所有内容都看起来不错，请进入管道设计器，并将数据流放入管道活动中。 调试管道以进行端到端测试。

## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：
*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [数据工厂的堆栈溢出论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [有关数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
*  [ADF 映射数据流 性能指南](concepts-data-flow-performance.md)

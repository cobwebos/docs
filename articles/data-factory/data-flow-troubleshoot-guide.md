---
title: 排查数据流问题
description: 了解如何排查 Azure 数据工厂中的数据流问题。
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: 2edd5b661240b6156cf8a02059b2b9a668c402f3
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829114"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>排查 Azure 数据工厂中的数据流问题

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文探讨了排查 Azure 数据工厂中的数据流问题的常见方法。

## <a name="common-errors-and-messages"></a>常见错误和消息

### <a name="error-code-df-executor-sourceinvalidpayload"></a>错误代码:DF-Executor-SourceInvalidPayload
- **消息**：数据预览、调试和管道数据流执行失败，因为容器不存在
- 原因：当数据集包含存储中不存在的容器时
- **建议**：请确保数据集中引用的容器存在或可访问。

### <a name="error-code-df-executor-systemimplicitcartesian"></a>错误代码:DF-Executor-SystemImplicitCartesian

- **消息**：用于 INNER 联接的隐式笛卡尔乘积不受支持。请改用 CROSS JOIN。 联接中使用的列应为行创建唯一键。
- 原因：用于逻辑计划之间 INNER 联接的隐式笛卡尔乘积不受支持。 如果联接中使用的列创建了唯一键，则需要来自关系双方的至少一列。
- **建议**：对于基于不相等的联接，必须选择使用 CUSTOM CROSS JOIN。

### <a name="error-code-df-executor-systeminvalidjson"></a>错误代码:DF-Executor-SystemInvalidJson

- **消息**：JSON 分析错误、编码不受支持或多行
- 原因：JSON 文件可能存在的问题：编码不受支持、字节损坏或在多个嵌套行上使用 JSON 源作为单文档
- **建议**：请验证 JSON 文件的编码是否受支持。 在使用 JSON 数据集的源转换上，展开“JSON 设置”，然后启用“单文档”。
 
### <a name="error-code-df-executor-broadcasttimeout"></a>错误代码:DF-Executor-BroadcastTimeout

- **消息**：广播联接超时错误。请确保在调试运行中广播流在 60 秒内生成数据，在作业运行中广播流在 300 秒内生成数据
- 原因：在调试运行中，广播的默认超时为 60 秒；在作业运行中，广播的默认超时为 300 秒。 为广播选择的流似乎很大，无法在此限制内生成数据。
- **建议**：请检查数据流转换的“优化”选项卡，以进行 Join、Exists 和 Lookup。 广播的默认选项为“自动”。 如果已设置此选项，或你要手动将左侧或右侧设置为“固定”下的广播，则可以设置较大的 Azure Integration Runtime 配置，或关闭广播。 为了获得最佳的数据流性能，推荐的方法是允许 Spark 使用“自动”进行广播，并使用内存优化的 Azure IR。

### <a name="error-code-df-executor-conversion"></a>错误代码:DF-Executor-Conversion

- **消息**：无法转换为日期或时间，因为字符无效
- 原因：数据未采用预期格式
- **建议**：请使用正确的数据类型

### <a name="error-code-df-executor-invalidcolumn"></a>错误代码:DF-Executor-InvalidColumn

- **消息**：需要在查询中指定列名称。如果使用的是 SQL 函数，请设置别名
- 原因：未指定列名称
- **建议**：如果使用的是 min()/max() 等 SQL 函数，请设置别名。

### <a name="error-code-getcommand-outputasync-failed"></a>错误代码:GetCommand OutputAsync 失败

- **消息**：在数据流调试和数据预览期间：GetCommand OutputAsync 失败，出现...
- 原因：这是后端服务错误。 可以重试此操作，并重启调试会话。
- **建议**：如果重试操作和重启会话无法解决问题，请联系客户支持。

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>错误代码:遇到了意外异常，执行失败

- **消息**：在数据流活动执行期间：遇到了意外异常，执行失败。
- 原因：这是后端服务错误。 可以重试此操作，并重启调试会话。
- **建议**：如果重试操作和重启会话无法解决问题，请联系客户支持。

## <a name="general-troubleshooting-guidance"></a>一般故障排除指南

1. 检查数据集连接状态。 在每个源转换和接收器转换中，访问要使用的每个数据集的链接服务，然后测试连接。
1. 在数据流设计器中，检查文件和表连接的状态。 打开“调试”并单击源转换上的“数据预览”，以确保你能够访问数据。
1. 如果数据预览中的一切正常，请转到管道设计器，并将数据流置于管道活动中。 调试管道，以进行端到端测试。

## <a name="next-steps"></a>后续步骤

若要获取故障排除方面的更多帮助，请尝试参阅以下资源：
*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 问题页](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
*  [ADF 映射数据流性能指南](concepts-data-flow-performance.md)

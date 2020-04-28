---
title: 数据流故障排除
description: 了解如何对 Azure 数据工厂中的数据流问题进行故障排除。
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: c9ac8d7ea465a26d29bf8f8fbc15dcefaf9d7575
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187273"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Azure 数据工厂中的数据流故障排除

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文探讨了 Azure 数据工厂中数据流的常见故障排除方法。

## <a name="common-errors-and-messages"></a>常见错误和消息

### <a name="error-code-df-executor-sourceinvalidpayload"></a>错误代码： DF-执行器-SourceInvalidPayload
- **消息**：由于容器不存在，数据预览、调试和管道数据流执行失败
- **原因**：数据集包含存储中不存在的容器
- **建议**：确保数据集中引用的容器存在或可访问。

### <a name="error-code-df-executor-systemimplicitcartesian"></a>错误代码： DF-执行器-SystemImplicitCartesian

- **消息**：不支持对内部联接使用隐式笛卡尔积，请改用交叉联接。 联接中使用的列应为行创建唯一键。
- **原因**：不支持逻辑计划之间的内部联接的隐式笛卡尔积。 如果联接中使用的列创建唯一键，则至少需要从关系的双方中有一列。
- **建议**：对于基于不相等的联接，你必须选择 "自定义交叉联接"。

### <a name="error-code-df-executor-systeminvalidjson"></a>错误代码： DF-执行器-SystemInvalidJson

- **消息**： JSON 分析错误，不支持的编码或多行
- **原因**： JSON 文件的可能问题：不支持的编码、损坏的字节或使用 json 源作为多个嵌套行上的单个文档
- **建议**：验证是否支持 JSON 文件的编码。 在使用 JSON 数据集的源转换上，展开 "JSON 设置" 并打开 "单一文档"。
 
### <a name="error-code-df-executor-broadcasttimeout"></a>错误代码： DF-执行器-BroadcastTimeout

- **消息**：广播联接超时错误，请确保在调试运行时，广播流在60秒内生成数据，并在作业运行期间300秒生成数据
- **原因**：广播的默认超时为60秒，在调试运行期间为300秒。 为广播选择的流似乎很大，无法在此限制内生成数据。
- **建议**：对于 Join、Exists 和 Lookup，请检查数据流转换的 "优化" 选项卡。 广播的默认选项为 "自动"。 如果设置了此选项，或者要在 "固定" 下手动设置向左或向右广播，则可以设置较大的 Azure Integration Runtime 配置，或关闭广播。 为实现最佳性能，建议使用 "自动" 方法，并使用内存优化 Azure IR。

### <a name="error-code-df-executor-conversion"></a>错误代码： DF-执行器转换

- **消息**：由于无效字符，转换为日期或时间失败
- **原因**：数据的格式不正确
- **建议**：使用正确的数据类型

### <a name="error-code-df-executor-invalidcolumn"></a>错误代码： DF-执行器-InvalidColumn

- **消息**：需要在查询中指定列名，请在使用 SQL 函数时设置别名
- **原因**：未指定列名称
- **建议**：如果使用 SQL 函数（例如 min （）/max （）等），请设置别名。

### <a name="error-code-getcommand-outputasync-failed"></a>错误代码： GetCommand OutputAsync 失败

- **消息**：在数据流调试和数据预览期间： GetCommand OutputAsync 失败，出现以下情况 .。。
- **原因**：这是后端服务错误。 可以重试该操作，也可以重新启动调试会话。
- **建议**：如果重试并重启不能解决问题，请与客户支持联系。

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>错误代码：遇到意外异常，执行失败

- **消息**：在数据流活动执行期间：遇到意外异常，执行失败。
- **原因**：这是后端服务错误。 可以重试该操作，也可以重新启动调试会话。
- **建议**：如果重试并重启不能解决问题，请与客户支持联系。

## <a name="general-troubleshooting-guidance"></a>一般故障排除指南

1. 检查数据集连接的状态。 在每个源和接收器转换中，访问使用的每个数据集的链接服务并测试连接。
1. 从数据流设计器检查文件和表连接的状态。 切换调试，并单击源转换上的 "数据预览"，以确保能够访问数据。
1. 如果所有内容都从数据预览中看起来很好，请进入管道设计器，并将数据流置于管道活动中。 调试用于端到端测试的管道。

## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：
*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [用于数据工厂的 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [有关数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
*  [ADF 映射数据流性能指南](concepts-data-flow-performance.md)

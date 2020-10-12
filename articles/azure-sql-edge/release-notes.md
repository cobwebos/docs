---
title: Azure SQL Edge 发行说明
description: 发行说明详细介绍了 Azure SQL Edge 映像中的新增功能或已更改的内容
keywords: 发行说明 SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: afd78acadf133a9f128eec402eba9d0eed51b8e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91284476"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge 发行说明 

本文介绍每个新的 Azure SQL Edge 内部版本中的新增功能或已更改的内容。

## <a name="azure-sql-edge---100-rtm"></a>Azure SQL Edge - 1.0.0 (RTM)

### <a name="sql-engine-build-number---15020001552"></a>SQL 引擎内部版本号 - 15.0.2000.1552

### <a name="whats-new"></a>新增功能
1. 基于 Ubuntu 18.04 的容器映像。 
2. 支持包含 `LAST_VALUE()` 和 `FIRST_VALUE()` 函数的 `IGNORE NULL` 和 `RESPECT NULL` 语法。 
3. 针对 PREDICT with ONNX 的可靠性改进。
4. 支持基于数据保留策略的清理。
   - 环形缓冲区支持用于故障排除的保留清理任务。
5. 新功能支持 
   - 快速恢复
   - 自动优化查询
   - 启用并行执行方案
6. 针对低功耗模式的节能改进
7. 流式处理新功能支持 
   - [快照窗口](https://docs.microsoft.com/stream-analytics-query/snapshot-window-azure-stream-analytics)：一种新的窗口类型，允许按同一时间到达的事件进行分组。 
   - 启用 [TopOne](https://docs.microsoft.com/stream-analytics-query/topone-azure-stream-analytics) 和 [CollectTop](https://docs.microsoft.com/stream-analytics-query/collecttop-azure-stream-analytics) 作为分析函数，这将允许返回按所选列排序的记录，而无需成为窗口的一部分。 
   - 对 [MATCH_RECOGNIZE](https://docs.microsoft.com/stream-analytics-query/match-recognize-stream-analytics) 的改进。 

### <a name="fixes"></a>修复项
1. 用于排查 TSQL 流式处理操作问题的附加错误消息和详细信息。 
2. 为了在空闲模式下维持电池寿命进行的改进。 
3. TSQL 流式处理引擎修复： 
   - 清理了已停止的流式处理作业 
   - 修复了本地化问题并改进了 unicode 处理方式
   - 提高了 Edge TSQL 流式处理的可调试性，允许用户从 get_streaming_job 查询作业失败错误。
4. 基于数据保留策略的清理
   - 修复了保留策略创建和清理方案。
5. 修复了后台计时器任务，以提高低功耗模式下的节能能力。

### <a name="known-issues"></a>已知问题 
1. 无法在计算列中使用 Date_Bucket T-SQL 函数。


## <a name="ctp-23"></a>CTP 2.3
### <a name="sql-engine-build-number---15020001549"></a>SQL 引擎内部版本号 - 15.0.2000.1549
### <a name="whats-new"></a>新增功能
1. 支持 Date_Bucket() 函数中的自定义原点。 
2. 在 SQL 部署中支持 BacPac 文件。
3. 支持基于数据保留策略的清理。      
   - DDL 支持启用保留策略 
   - 清理存储过程和后台清理任务
   - 用于监视清理任务的扩展事件

### <a name="fixes"></a>修复项
1. 用于排查 TSQL 流式处理操作问题的附加错误消息和详细信息。 
2. 为了在空闲模式下维持电池寿命进行的改进。 
3. TSQL 流式处理引擎修复： 
   - 修复了子流跳跃窗口的卡水印问题 
   - 修复了框架异常处理，以确保将其收集为用户可操作的错误


## <a name="ctp-22"></a>CTP 2.2
### <a name="sql-engine-build-number---15020001546"></a>SQL 引擎内部版本号 - 15.0.2000.1546
### <a name="whats-new"></a>新增功能
1. 支持非根容器 
2. 支持使用情况和诊断数据收集 
3. T-SQL 流式处理更新
   - 支持流对象名称的 Unicode 字符

### <a name="fixes"></a>修复项
1. T-SQL 流式处理更新
   - 进程清理改进
   - 日志记录和诊断改进
2. 针对数据引入的性能改进

## <a name="ctp-21"></a>CTP 2.1 
### <a name="sql-engine-build-number---15020001545"></a>SQL 引擎内部版本号 - 15.0.2000.1545
### <a name="fixes"></a>修复项
1. 修复了 PREDICT with ONNX 模型，以处理 ARM 中的 CPUID 问题 
2. 修复以改进 TSQL 流式处理启动时对故障路径的处理 
3. 修复了没有数据时，作业指标中水印延迟的错误值。 
4. 修复了输出适配器在批之间具有可变架构时出现的问题。  

## <a name="ctp-20"></a>CTP 2.0 
### <a name="sql-engine-build-number---15020001401"></a>SQL 引擎内部版本号 - 15.0.2000.1401
### <a name="whats-new"></a>新增功能
1.  产品名称更新为“Azure SQL Edge”
1.  Date_bucket 函数

    i.  支持日期、时间、日期/时间类型
3.  PREDICT with ONNX
    
    i.  ONNX 需要 RUNTIME 参数 
    
4.  TSQL 流式处理支持（受限预览版） 
 
### <a name="known-issues"></a>已知问题

1. <b>问题：</b>如果在启动时应用 dacpac，可能会因计时问题而出现故障。

    <b>解决方法：</b>重新启动 SQL Server 或容器将重试应用 dacpac，应该可以解决此问题
### <a name="request-support"></a>请求支持
1. 可以在[支持页面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)中请求支持。

4. 确保选择以下字段： 
    * 问题类型 - 技术 
    * 服务 - IoT Edge
    * 问题类型 - 我的问题与 IoT Edge 模块有关
    * 问题子类型 - Azure SQL Edge

   ![支持票证示例](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1.5
### <a name="sql-engine-build-number---15020001331"></a>SQL 引擎内部版本号 - 15.0.2000.1331
### <a name="whats-new"></a>新增功能
1. Date_bucket 函数
    
    i. 支持 DateTimeOffset 类型
2. PREDICT with ONNX 模型

    i. nvarchar 支持
 
## <a name="ctp-14"></a>CTP 1.4
### <a name="sql-engine-build-number---15020001247"></a>SQL 引擎内部版本号 - 15.0.2000.1247
### <a name="whats-new"></a>新增功能
1.  PREDICT with ONNX 模型
 
    i.  Varchar 支持
    
    ii. 迁移到 ONNX 运行时版本 1.0 
2.  功能支持 - 启用了以下功能：

    i.   CDC 支持

    ii.  带压缩功能的历史记录表

    iii. 提高预读日志的比例因子

    iv.  批处理模式 ES 筛选器下推

    v.   预读优化
 
## <a name="ctp-13"></a>CTP 1.3
### <a name="sql-engine-build-number---15020001147"></a>SQL 引擎内部版本号 - 15.0.2000.1147
### <a name="whats-new"></a>新增功能
1. Azure IOT 门户部署 

    i.   支持部署 AMD64 和 ARM 映像

    ii.  支持创建流式处理作业

    iii. Dacpac 部署
2. PREDICT with ONNX 模型

    i. 数值类型支持
3. 功能支持 - 启用了以下功能：

    i.  将聚合下推到列存储扫描

    ii. 旋转木马式扫描
4. 占用空间和内存消耗缩减工作

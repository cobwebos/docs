---
title: Azure SQL Edge 发行说明
description: 详细说明了 Azure SQL Edge 映像中新功能或更改内容的发行说明
keywords: 发行说明 SQL 边缘
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 3306e51fe2fdbb2586be9684432d8f8c310afe95
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90900591"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge 发行说明 

本文介绍 Azure SQL Edge 的每个新版本的新增功能和变化。

## <a name="azure-sql-edge---100-rtm"></a>Azure SQL Edge-1.0.0 (RTM) 

### <a name="sql-engine-build-number---15020001549"></a>SQL 引擎内部版本号-15.0.2000.1549

### <a name="whats-new"></a>新增功能
1. 基于 Ubuntu 18.04 的容器映像。 
2. 和函数的支持 `IGNORE NULL` 和 `RESPECT NULL` 语法 `LAST_VALUE()` `FIRST_VALUE()` 。 
3. 通过 ONNX 预测的可靠性改进。
4. 支持基于数据保留策略的清理。      
   - 支持优化清理聚集列存储索引。
5. 新功能支持 
   - 快速恢复
   - 自动优化查询

### <a name="fixes"></a>修复项
1. 用于对 TSQL 流式处理操作进行疑难解答的其他错误消息和详细信息。 
2. 改进以保持空闲模式下的电池寿命。 
3. TSQL 流式处理引擎修复： 
   - 清除已停止流式处理作业 
   - 用于本地化和 unicode 处理改进的修补程序
4. 基于数据保留策略的清除
   - 保留策略创建和清理方案的修补程序。
5. 修复了后台计时器任务，以提高低功耗模式下的节能能力。


## <a name="ctp-23"></a>CTP 2.3
### <a name="sql-engine-build-number---15020001549"></a>SQL 引擎内部版本号-15.0.2000.1549
### <a name="whats-new"></a>新增功能
1. 支持 Date_Bucket ( # A1 函数中的自定义源。 
2. 支持作为 SQL 部署的一部分的 BacPac 文件。
3. 支持基于数据保留策略的清理。      
   - 启用保留策略的 DDL 支持 
   - 清除存储过程和后台清除任务
   - 用于监视清理任务的扩展事件

### <a name="fixes"></a>修复项
1. 用于对 TSQL 流式处理操作进行疑难解答的其他错误消息和详细信息。 
2. 改进以保持空闲模式下的电池寿命。 
3. TSQL 流式处理引擎修复： 
   - 解决 substreamed 跳跃窗口的停滞水印问题 
   - 修复框架异常处理，以确保将其收集为用户可操作错误


## <a name="ctp-22"></a>CTP 2.2
### <a name="sql-engine-build-number---15020001546"></a>SQL 引擎内部版本号-15.0.2000.1546
### <a name="whats-new"></a>新增功能
1. 支持非根容器 
2. 支持使用情况和诊断数据收集 
3. T-sql 流式处理更新
   - 支持对流对象名称的 Unicode 字符

### <a name="fixes"></a>修复项
1. T-sql 流式处理更新
   - 进程清理改进
   - 日志记录和诊断改进
2. 数据引入性能改进

## <a name="ctp-21"></a>CTP 2.1 
### <a name="sql-engine-build-number---15020001545"></a>SQL 引擎内部版本号-15.0.2000.1545
### <a name="fixes"></a>修复项
1. 修复 with ONNX 模型以处理 ARM 中的 CPUID 问题 
2. 解决此问题，以便在启动 TSQL 流式处理时改进失败路径的处理 
3. 当没有任何数据时，修复作业指标中水印延迟的错误值。 
4. 当适配器在批处理之间具有可变架构时，请解决输出适配器问题。  

## <a name="ctp-20"></a>CTP 2.0 
### <a name="sql-engine-build-number---15020001401"></a>SQL 引擎内部版本号-15.0.2000.1401
### <a name="whats-new"></a>新增功能
1.  产品名称已更新为 "Azure SQL Edge"
1.  Date_bucket 函数

    i.  支持日期、时间、日期时间类型
3.  用 ONNX 进行预测
    
    i.  ONNX 所需的运行时参数 
    
4.  TSQL 流式处理支持 (有限预览)  
 
### <a name="known-issues"></a>已知问题

1. <b>问题：</b> 由于计时问题而导致在启动时应用 dacpac 的潜在故障。

    <b>解决方法：</b> 重新启动 SQL Server 或容器将重试应用 dacpac，并应修复问题
### <a name="request-support"></a>请求支持
1. 可以在 [支持页](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)中请求支持。

4. 确保选择以下字段： 
    * 问题类型-技术 
    * 服务-IoT Edge
    * 问题类型-我的问题与 IoT Edge 模块相关
    * 问题子类型-Azure SQL Edge

   ![示例支持票证](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1。5
### <a name="sql-engine-build-number---15020001331"></a>SQL 引擎内部版本号-15.0.2000.1331
### <a name="whats-new"></a>新增功能
1. Date_bucket 函数
    
    i. 对 DateTimeOffset 类型的支持
2. 通过 ONNX 模型进行预测

    i. nvarchar 支持
 
## <a name="ctp-14"></a>CTP 1。4
### <a name="sql-engine-build-number---15020001247"></a>SQL 引擎内部版本号-15.0.2000.1247
### <a name="whats-new"></a>新增功能
1.  通过 ONNX 模型进行预测
 
    i.  Varchar 支持
    
    ii. 迁移到 ONNX 运行时版本1。0 
2.  功能支持-启用了以下功能：

    i.   CDC 支持

    ii.  带压缩的历史记录表

    iii. 更高的日志读取比例

    iv.  批处理模式 ES 筛选器下推

    v.   预读优化
 
## <a name="ctp-13"></a>CTP 1。3
### <a name="sql-engine-build-number---15020001147"></a>SQL 引擎内部版本号-15.0.2000.1147
### <a name="whats-new"></a>新增功能
1. Azure IOT 门户部署 

    i.   支持部署 AMD64 和 ARM 映像

    ii.  支持流式作业创建

    iii. Dacpac 部署
2. 通过 ONNX 模型进行预测

    i. 数值类型支持
3. 功能支持-启用了以下功能：

    i.  向下推聚合到列存储扫描

    ii. 快乐的循环扫描
4. 占用空间和内存消耗缩减工作

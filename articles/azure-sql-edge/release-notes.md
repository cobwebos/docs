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
ms.date: 07/27/2020
ms.openlocfilehash: 74e9772ada010d79e81ef36cae89ba586db73077
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553404"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge 发行说明 

本文介绍 Azure SQL Edge 的每个新版本的新增功能和变化。

## <a name="ctp-22"></a>CTP 2.2
### <a name="sql-engine-build-number---15020001546"></a>SQL 引擎内部版本号-15.0.2000.1546
### <a name="fixes"></a>修复项
1. 支持非根容器 
2. 支持使用情况和诊断数据收集 
3. T-sql 流式处理更新
   - 支持对流对象名称的 Unicode 字符
   - 进程清理改进
   - 日志记录和诊断改进
4. 数据引入性能改进

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

1. <b>问题：</b>由于计时问题而导致在启动时应用 dacpac 的潜在故障。

    <b>解决方法：</b>重新启动 SQL Server 或容器将重试应用 dacpac，并应修复问题
### <a name="request-support"></a>请求支持
1. 可以在[支持页](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)中请求支持。

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

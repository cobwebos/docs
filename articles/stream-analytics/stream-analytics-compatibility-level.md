---
title: "了解 Azure 流分析作业的兼容性级别。 | Microsoft Docs"
description: "了解如何设置 Azure 流分析作业的兼容性级别，并了解最新兼容性级别中的重大更改"
keywords: "兼容性级别, 流式处理数据"
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: kfile
editor: 
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/03/2018
ms.author: sngun
ms.openlocfilehash: 0d89259d54fba0bd57881ec69cb61b5af6d603b5
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure 流分析作业的兼容性级别
 
兼容性级别是指 Azure 流分析服务特定于版本的行为。 Azure 流分析是托管的服务，会定期进行功能更新和性能改进。 通常情况下，更新自动提供给最终用户。 但是，某些新功能可能会引入重大更改，例如，更改现有作业的行为、更改使用这些作业的数据的进程，等等。兼容性级别用于表示在流分析中引入的重大更改。 重大更改始终随新的兼容性级别一起引入。 

兼容性级别可确保现有作业运行而不会有任何失败。 创建新的流分析作业时，最佳做法是使用可供使用的最新兼容性级别来创建它。 
 
## <a name="set-a-compatibility-level"></a>设置兼容性级别 

兼容性级别控制流分析作业的运行时行为。 可以通过门户或通过[创建作业 REST API 调用](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)来设置流分析作业的兼容性级别。 Azure 流分析目前支持两种兼容性级别 -“1.0”和“1.1”。 默认情况下，兼容性级别设置为“1.0”，这是在 Azure 流分析公开发行期间引入的。 若要更新默认值，请导航到现有的流分析作业 > 在“配置”部分选择“兼容性级别”选项，然后更改该值。 

请确保在更新兼容性级别之前停止作业。 如果作业处于运行状态，则无法更新兼容性级别。 

![门户中的兼容性级别](media\stream-analytics-compatibility-level/image1.png)

 
在更新兼容性级别时，T-SQL 编译器会使用与所选兼容性级别相对应的语法来验证作业。 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>最新兼容性级别 (1.1) 中的重大更改

在兼容性级别 1.1 中引入了以下重大更改：

* **服务总线 XML 格式**  

  * **以前的版本：**Azure 流分析使用 DataContractSerializer，因此消息内容包括 XML 标记。 例如：
    
   @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ “SensorId”:”1”, “Temperature”:64\}\u0001 

  * **当前版本：**消息内容只包含流，没有其他的标记。 例如：
  
   { “SensorId”:”1”, “Temperature”:64} 
 
* **字段名称保留区分大小写的做法**  

  * **以前的版本：**在由 Azure 流分析引擎处理时，字段名称更改为小写。 

  * **当前版本：**在由 Azure 流分析引擎处理时，字段名称保留区分大小写的做法。 
 
* **FloatNaNDeserializationDisabled**  

  * **以前的版本：**CREATE TABLE 命令不使用 NaN（非数值。 例如，Infinity、-Infinity）在 FLOAT 列类型中筛选事件，因为对于这些数字来说，事件不在已记录的范围之内。

  * **当前版本：**CREATE TABLE 允许指定强架构。 流分析引擎验证数据是否符合此架构。 使用这一模型，该命令可以通过 NaN 值筛选事件。 

* **禁止在 JSON 中对日期/时间字符串进行自动向上转换。**  

  * **以前的版本：**JSON 分析器会自动将包含日期/时间/时区信息的字符串值向上转换为日期/时间类型，然后转换为 UTC。 这导致丢失时区信息。

  * **当前版本：**不再将包含日期/时间/时区信息的字符串值自动向上转换为日期/时间类型。 因此会保留时区信息。 

## <a name="next-steps"></a>后续步骤
* [Azure 流分析故障排除指南](stream-analytics-troubleshooting-guide.md)
* [流分析“资源运行状况”边栏选项卡](stream-analytics-resource-health.md)
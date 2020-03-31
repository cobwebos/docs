---
title: include 文件
description: include 文件
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79538826"
---
### <a name="the-data-model-of-the-schema"></a>架构的数据模型

|字段|描述|
|----|----|
|**警报名称**|警报显示名称|
|**AlertType**|唯一警报标识符|
|**置信度**|（可选）此警报的置信度（高/低）|
|**信心评分**|（可选）安全警报的数字置信度指示器|
|**说明**|警报的说明文本|
|**显示名称**|警报的显示名称|
|**结束时间**|警报的影响结束时间（导致警报的最后一个事件的时间）|
|**实体**|与警报相关的实体列表。 此列表可以容纳不同类型的实体的混合|
|**扩展链接**|（可选）指向与警报相关的所有链接的袋子。 这个袋子可以容纳不同类型的链接的混合物|
|**扩展属性**|与警报相关的一袋其他字段|
|**正在发生**|确定警报是事件还是常规警报。 事件是一个安全警报，将多个警报聚合到一个安全事件中|
|**ProcessingEndTime**|创建警报的 UTC 时间戳|
|**产品组件名称**|（可选）生成警报的产品中组件的名称。|
|ProductName****|常量（"Azure 安全中心"）|
|**提供商名称**|unused|
|**补救步骤**|修复安全威胁的手动操作项|
|**资源 Id**|受影响资源的完整标识符|
|**严重性**|警报严重性（高/中/低/信息）|
|**SourceComputerId**|受影响服务器的唯一 GUID（如果在服务器上生成警报）|
|**SourceSystem**|unused|
|**StartTime**|警报的影响开始时间（导致警报的第一个事件的时间）|
|**系统警报 Id**|此安全警报实例的唯一标识符|
|**租户 Id**|扫描资源所在的订阅的父 Azure 活动目录租户的标识符|
|**TimeGenerated**|进行评估的 UTC 时间戳（安全中心的扫描时间）（与发现时间UTC 相同）|
|**类型**|常数（"安全警报"）|
|**供应商名称**|提供警报的供应商的名称（例如"Microsoft"）|
|**供应商原始 ID**|unused|
|**工作区资源组**|如果警报是在报告到工作区的 VM、服务器、虚拟机缩放集或应用服务实例上生成的，则包含该工作区资源组名称|
|**工作区订阅 Id**|如果警报是在报告到工作区的 VM、服务器、虚拟机缩放集或应用服务实例上生成的，则包含该工作区订阅 Id|
|||

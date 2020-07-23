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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "79538826"
---
### <a name="the-data-model-of-the-schema"></a>架构的数据模型

|字段|说明|
|----|----|
|**AlertName**|警报显示名称|
|**AlertType**|唯一警报标识符|
|**ConfidenceLevel**|可有可无此警报的置信度（高/低）|
|**ConfidenceScore**|可有可无安全警报的数字置信指标|
|**说明**|警报的描述文本|
|**DisplayName**|警报的显示名称|
|**结束**|警报的影响结束时间（构成警报的最后一个事件的时间）|
|**实体**|与警报相关的实体的列表。 此列表可以混合使用各种类型的实体|
|**ExtendedLinks**|可有可无与警报相关的所有链接的袋。 此袋可以保存各种类型的链接|
|**ExtendedProperties**|与警报相关的附加字段包|
|**IsIncident**|确定警报是事件还是常规警报。 事件是将多个警报聚合为一个安全事件的安全警报|
|**ProcessingEndTime**|在其中创建警报的 UTC 时间戳|
|**ProductComponentName**|可有可无生成警报的产品内的组件的名称。|
|**ProductName**|常量（"Azure 安全中心"）|
|**ProviderName**|unused|
|**RemediationSteps**|手动纠正安全威胁的操作项|
|**ResourceId**|受影响资源的完整标识符|
|**严重性**|警报严重性（高/中/低/信息）|
|**SourceComputerId**|受影响服务器的唯一 GUID （如果在服务器上生成警报）|
|**SourceSystem**|unused|
|**StartTime**|警报的影响开始时间（构成警报的第一个事件的时间）|
|**SystemAlertId**|此安全警报实例的唯一标识符|
|**TenantId**|扫描的资源所在的订阅的父 Azure Active directory 租户的标识符|
|**TimeGenerated**|发生评估的 UTC 时间戳（安全中心扫描时间）（等同于 DiscoveredTimeUTC）|
|**Type**|常量（"SecurityAlert"）|
|**VendorName**|提供警报的供应商的名称（例如 "Microsoft"）|
|**VendorOriginalId**|unused|
|**WorkspaceResourceGroup**|如果在向工作区报告的 VM、服务器、虚拟机规模集或应用服务实例上生成警报，则包含该工作区资源组名称|
|**WorkspaceSubscriptionId**|如果在向工作区报告的 VM、服务器、虚拟机规模集或应用服务实例上生成警报，则包含该工作区 subscriptionId|
|||

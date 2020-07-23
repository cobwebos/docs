---
title: 在 Azure API for FHIR® 中启用诊断日志记录
description: 本文介绍如何在 Azure API for FHIR® 中启用诊断日志记录
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/01/2019
ms.openlocfilehash: 948ca03b5bf503c884df5df56c61951b381874a9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870867"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>在 Azure API for FHIR® 中启用诊断日志记录

本文介绍如何在 Azure API for FHIR® 中启用诊断日志记录，以及如何查看这些日志的一些示例查询。 对于必须符合法规与管制要求的任何医疗保健服务（例如 HIPAA）而言，访问诊断日志是必不可少的。 Azure API for FHIR® 中用于启用诊断日志的功能是 Azure 门户中的[诊断](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)设置。 

## <a name="enable-audit-logs"></a>启用审核日志
1. 若要在 Azure API for FHIR® 中启用诊断日志记录，请在 Azure 门户中选择你的 Azure API for FHIR® 服务 
2. 导航到“诊断设置” **** 诊断设置 
![](media/diagnostic-logging/diagnostic-settings-screen.png) 

3. 选择“+ 添加诊断设置” 

4. 输入设置名称

5. 选择用于访问诊断日志的方法：

    1. 存档到存储帐户以进行审核或手动检查。  要使用的存储帐户需已事先创建。
    2. 流式传输到事件中心，供第三方服务或自定义分析解决方案引入。  在配置此步骤之前，需先创建事件中心命名空间和事件中心策略。
    3. 流式传输到 Azure Monitor 中的 Log Analytics 工作区。  在选择此选项之前，需先创建 Logs Analytics 工作区。

6. 选择“AuditLogs”以及要捕获的任何指标 

7. 点击“保存”

> [!Note] 
> 最初的几条日志最长可能需要在 15 分钟后才会显示在 Log Analytics 中。  
 
有关如何使用诊断日志的详细信息，请参阅 [Azure 资源日志文档](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview)

## <a name="audit-log-details"></a>审核日志详细信息
此时，Azure API for FHIR® 服务将在审核日志中返回以下字段： 

|字段名称  |类型  |说明  |
|---------|---------|---------|
|CallerIdentity|动态|包含标识信息的泛型属性包
|CallerIdentityIssuer|String|颁发者 
|CallerIdentityObjectId|String|Object_Id 
|CallerIPAddress|String|调用方的 IP 地址 
|CorrelationId|String| 相关性 ID
|FhirResourceType|String|对其执行了操作的资源类型
|LogCategory|String|日志类别（目前返回“AuditLogs”LogCategory）
|位置|String|处理请求的服务器所在的位置（例如“美国中南部”）
|OperationDuration|Int|完成此请求所花费的时间（秒）
|OperationName|String| 描述操作类型（例如更新、搜索类型）
|RequestUri|String|请求 URI 
|ResultType|String|当前可用值为 Started、Succeeded 或 Failed   
|StatusCode|Int|HTTP 状态代码。 （例如 200） 
|TimeGenerated|DateTime|事件的日期和时间|
|属性|String| 描述 fhirResourceType 的属性
|SourceSystem|String| 源系统（在本例中始终为“Azure”）
|TenantId|String|租户 ID
|类型|String|日志类型（在本例中始终为 MicrosoftHealthcareApisAuditLog）
|_ResourceId|String|有关资源的详细信息

## <a name="sample-queries"></a>示例查询

下面是可用于浏览日志数据的几个基本 Application Insights 查询。

运行此查询可查看最近的 100 条日志： 

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

运行此查询可按“FHIR 资源类型”将操作分组： 

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

运行此查询可获取所有失败结果 

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>结束语 
访问诊断日志对于监视服务和提供合规性报告至关重要。 Azure API for FHIR® 允许通过诊断日志执行这些操作。 
 
FHIR® 是 HL7 的注册商标，在 HL7 的许可下使用。

## <a name="next-steps"></a>后续步骤
本文介绍了如何为 Azure API for FHIR® 启用审核日志。 接下来，请了解可在 Azure API for FHIR 中配置的其他设置
 
>[!div class="nextstepaction"]
>[其他设置](azure-api-for-fhir-additional-settings.md)
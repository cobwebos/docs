---
title: 基于工作区的 Azure Monitor Application Insights 资源架构
description: 了解 Azure Monitor Application Insights 基于工作区的资源的新表结构和架构。
ms.topic: conceptual
ms.date: 05/09/2020
ms.openlocfilehash: 8f0bee64d74cfd5b6abef5c918c023974fda3fcf
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931047"
---
# <a name="workspace-based-resource-changes-preview"></a>基于工作区的资源更改 (预览) 

在引入 [基于工作区的 Application Insights 资源](create-workspace-resource.md)之前，Application Insights 数据与 Azure Monitor 中的其他日志数据单独存储。 两者都基于 Azure 数据资源管理器，并使用相同的 Kusto 查询语言 (KQL) 。 Azure Monitor 中的 [日志](../platform/data-platform-logs.md)中对此进行了说明。

使用基于工作区的 Application Insights 资源数据存储在包含其他监视数据和应用程序数据的 Log Analytics 工作区中。 这可以简化配置，使你能够更轻松地跨多个解决方案分析数据并利用工作区的功能。

## <a name="table-structure"></a>表结构

| 旧表名称 | 新表名称 | 说明 |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  可用性测试中的摘要数据。|
| browserTimings | AppBrowserTimings | 有关客户端性能的数据，例如处理传入数据所用的时间。|
| 依赖项 | AppDependencies | 从应用程序到通过 TrackDependency() 记录的其他组件（包括外部组件）的调用 - 例如，对 REST API、数据库或文件系统的调用。  |
| customEvents | AppEvents | 应用程序创建的自定义事件。 |
| customMetrics | AppMetrics | 应用程序创建的自定义指标。 |
| pageViews | AppPageViews| 每个网站的浏览情况数据，以及浏览器信息。 |
| performanceCounters | AppPerformanceCounters | 支持应用程序的计算资源的性能度量，例如 Windows 性能计数器。 |
| 请求 | AppRequests | 应用程序收到的请求。 例如，为 Web 应用接收到的每个 HTTP 请求记录一条单独的请求记录。  |
| exceptions | AppSystemEvents | 应用程序运行时引发的异常捕获服务器端和客户端（浏览器）异常。 |
| traces | AppTraces | 通过 TrackTrace () 记录的应用程序代码/日志记录框架发出的详细日志（跟踪）。 |

## <a name="table-schemas"></a>表架构

以下部分显示了经典属性名称和基于新工作区的 Application Insights 属性名称之间的映射。  使用此信息可转换使用旧表的任何查询。

大多数列具有相同的名称，并且具有不同的大小写。 由于 KQL 区分大小写，因此需要更改每个列名以及现有查询中的表名。 突出显示除了大写之外的更改的列。 你仍可以在 Application Insights 资源的 " **日志** " 窗格内使用经典 Application Insights 查询，即使它是基于工作区的资源。 当从 Log Analytics 工作区体验的上下文中进行查询时，必须提供新的属性名。

### <a name="appavailabilityresults"></a>AppAvailabilityResults

旧表：可用性

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|字符串|\_ResourceGUID|字符串|
|application_Version|字符串|AppVersion|字符串|
|appName|字符串|\_ResourceId|字符串|
|client_Browser|字符串|ClientBrowser|字符串|
|client_City|字符串|ClientCity|字符串|
|client_CountryOrRegion|字符串|ClientCountryOrRegion|字符串|
|client_IP|字符串|ClientIP|字符串|
|client_Model|字符串|ClientModel|字符串|
|client_OS|字符串|ClientOS|字符串|
|client_StateOrProvince|字符串|ClientStateOrProvince|字符串|
|client_type|字符串|ClientType|字符串|
|cloud_RoleInstance|字符串|AppRoleInstance|字符串|
|cloud_RoleName|字符串|AppRoleName|字符串|
|customDimensions|动态|属性|动态|
|customMeasurements|动态|度量|动态|
|duration|real|DurationMs|real|
|`id`|字符串|`Id`|string|
|iKey|字符串|IKey|字符串|
|itemCount|int|ItemCount|int|
|itemId|字符串|\_ItemId|字符串|
|itemType|字符串|类型|字符串|
|location|字符串|位置|字符串|
|message|字符串|Message|字符串|
|name|字符串|名称|字符串|
|operation_Id|字符串|OperationId|字符串|
|operation_Name|字符串|OperationName|字符串|
|operation_ParentId|字符串|OperationParentId|字符串|
|operation_SyntheticSource|字符串|OperationSyntheticSource|字符串|
|performanceBucket|字符串|PerformanceBucket|字符串|
|sdkVersion|字符串|SdkVersion|字符串|
|session_Id|字符串|SessionId|字符串|
|大小|real|大小|real|
|success|字符串|成功|Bool|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|字符串|UserAccountId|字符串|
|user_AuthenticatedId|字符串|UserAuthenticatedId|字符串|
|user_Id|字符串|UserId|字符串|

### <a name="appbrowsertimings"></a>AppBrowserTimings

旧表： browserTimings

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|字符串|\_ResourceGUID|字符串|
|application_Version|字符串|AppVersion|字符串|
|appName|字符串|\_ResourceId|字符串|
|client_Browser|字符串|ClientBrowser|字符串|
|client_City|字符串|ClientCity|字符串|
|client_CountryOrRegion|字符串|ClientCountryOrRegion|字符串|
|client_IP|字符串|ClientIP|字符串|
|client_Model|字符串|ClientModel|字符串|
|client_OS|字符串|ClientOS|字符串|
|client_StateOrProvince|字符串|ClientStateOrProvince|字符串|
|client_type|字符串|ClientType|字符串|
|cloud_RoleInstance|字符串|AppRoleInstance|字符串|
|cloud_RoleName|字符串|AppRoleName|字符串|
|customDimensions|动态|属性|动态|
|customMeasurements|动态|度量|动态|
|iKey|字符串|IKey|字符串|
|itemCount|int|ItemCount|int|
|itemId|字符串|\_ItemId|字符串|
|itemType|字符串|类型|字符串|
|name|字符串|名称|datetime|
|networkDuration|real|NetworkDurationMs|real|
|operation_Id|字符串|OperationId|字符串|
|operation_Name|字符串|OperationName|字符串|
|operation_ParentId|字符串|OperationParentId|字符串|
|operation_SyntheticSource|字符串|OperationSyntheticSource|字符串|
|performanceBucket|字符串|PerformanceBucket|字符串|
|processingDuration|real|ProcessingDurationMs|real|
|receiveDuration|real|ReceiveDurationMs|real|
|sdkVersion|字符串|SdkVersion|字符串|
|sendDuration|real|SendDurationMs|real|
|session_Id|字符串|SessionId|字符串|
|timestamp|datetime|TimeGenerated|datetime|
|totalDuration|real|TotalDurationMs|real|
|url|字符串|Url|字符串|
|user_AccountId|字符串|UserAccountId|字符串|
|user_AuthenticatedId|字符串|UserAuthenticatedId|字符串|
|user_Id|字符串|UserId|字符串|

### <a name="appdependencies"></a>AppDependencies

旧表：依赖关系

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|字符串|\_ResourceGUID|字符串|
|application_Version|字符串|AppVersion|字符串|
|appName|字符串|\_ResourceId|字符串|
|client_Browser|字符串|ClientBrowser|字符串|
|client_City|字符串|ClientCity|字符串|
|client_CountryOrRegion|字符串|ClientCountryOrRegion|字符串|
|client_IP|字符串|ClientIP|字符串|
|client_Model|字符串|ClientModel|字符串|
|client_OS|字符串|ClientOS|字符串|
|client_StateOrProvince|字符串|ClientStateOrProvince|字符串|
|client_type|字符串|ClientType|字符串|
|cloud_RoleInstance|字符串|AppRoleInstance|字符串|
|cloud_RoleName|字符串|AppRoleName|字符串|
|customDimensions|动态|属性|动态|
|customMeasurements|动态|度量|动态|
|数据|字符串|数据|字符串|
|duration|real|DurationMs|real|
|`id`|字符串|`Id`|string|
|iKey|字符串|IKey|字符串|
|itemCount|int|ItemCount|int|
|itemId|字符串|\_ItemId|字符串|
|itemType|字符串|类型|字符串|
|name|字符串|名称|字符串|
|operation_Id|字符串|OperationId|字符串|
|operation_Name|字符串|OperationName|字符串|
|operation_ParentId|字符串|OperationParentId|字符串|
|operation_SyntheticSource|字符串|OperationSyntheticSource|字符串|
|performanceBucket|字符串|PerformanceBucket|字符串|
|resultCode|string|ResultCode|字符串|
|sdkVersion|字符串|SdkVersion|字符串|
|session_Id|字符串|SessionId|字符串|
|success|字符串|成功|Bool|
|目标|字符串|目标|string|
|timestamp|datetime|TimeGenerated|datetime|
|type|字符串|DependencyType|字符串|
|user_AccountId|字符串|UserAccountId|字符串|
|user_AuthenticatedId|字符串|UserAuthenticatedId|字符串|
|user_Id|字符串|UserId|字符串|

### <a name="appevents"></a>AppEvents

旧表： customEvents

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|字符串|\_ResourceGUID|字符串|
|application_Version|字符串|AppVersion|字符串|
|appName|字符串|\_ResourceId|字符串|
|client_Browser|字符串|ClientBrowser|字符串|
|client_City|字符串|ClientCity|字符串|
|client_CountryOrRegion|字符串|ClientCountryOrRegion|字符串|
|client_IP|字符串|ClientIP|字符串|
|client_Model|字符串|ClientModel|字符串|
|client_OS|字符串|ClientOS|字符串|
|client_StateOrProvince|字符串|ClientStateOrProvince|字符串|
|client_type|字符串|ClientType|字符串|
|cloud_RoleInstance|字符串|AppRoleInstance|字符串|
|cloud_RoleName|字符串|AppRoleName|字符串|
|customDimensions|动态|属性|动态|
|customMeasurements|动态|度量|动态|
|iKey|字符串|IKey|字符串|
|itemCount|int|ItemCount|int|
|itemId|字符串|\_ItemId|字符串|
|itemType|字符串|类型|字符串|
|name|字符串|名称|字符串|
|operation_Id|字符串|OperationId|字符串|
|operation_Name|字符串|OperationName|字符串|
|operation_ParentId|字符串|OperationParentId|字符串|
|operation_SyntheticSource|字符串|OperationSyntheticSource|字符串|
|sdkVersion|字符串|SdkVersion|字符串|
|session_Id|字符串|SessionId|字符串|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|字符串|UserAccountId|字符串|
|user_AuthenticatedId|字符串|UserAuthenticatedId|字符串|
|user_Id|字符串|UserId|字符串|

### <a name="appmetrics"></a>AppMetrics

旧表： customMetrics

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|字符串|\_ResourceGUID|字符串|
|application_Version|字符串|AppVersion|字符串|
|appName|字符串|\_ResourceId|字符串|
|client_Browser|字符串|ClientBrowser|字符串|
|client_City|字符串|ClientCity|字符串|
|client_CountryOrRegion|字符串|ClientCountryOrRegion|字符串|
|client_IP|字符串|ClientIP|字符串|
|client_Model|字符串|ClientModel|字符串|
|client_OS|字符串|ClientOS|字符串|
|client_StateOrProvince|字符串|ClientStateOrProvince|字符串|
|client_type|字符串|ClientType|字符串|
|cloud_RoleInstance|字符串|AppRoleInstance|字符串|
|cloud_RoleName|字符串|AppRoleName|字符串|
|customDimensions|动态|属性|动态|
|iKey|字符串|IKey|字符串|
|itemId|字符串|\_ItemId|字符串|
|itemType|字符串|类型|字符串|
|name|字符串|名称|字符串|
|operation_Id|字符串|OperationId|字符串|
|operation_Name|字符串|OperationName|字符串|
|operation_ParentId|字符串|OperationParentId|字符串|
|operation_SyntheticSource|字符串|OperationSyntheticSource|字符串|
|sdkVersion|字符串|SdkVersion|字符串|
|session_Id|字符串|SessionId|字符串|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|字符串|UserAccountId|字符串|
|user_AuthenticatedId|字符串|UserAuthenticatedId|字符串|
|user_Id|字符串|UserId|字符串|
|value|real|删除 () ||
|valueCount|int|ValueCount|int|
|valueMax|real|ValueMax|real|
|valueMin|real|ValueMin|real|
|valueStdDev|real|ValueStdDev|real|
|valueSum|real|ValueSum|real|

### <a name="apppageviews"></a>AppPageViews

旧表： pageViews

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|字符串|\_ResourceGUID|字符串|
|application_Version|字符串|AppVersion|字符串|
|appName|字符串|\_ResourceId|字符串|
|client_Browser|字符串|ClientBrowser|字符串|
|client_City|字符串|ClientCity|字符串|
|client_CountryOrRegion|字符串|ClientCountryOrRegion|字符串|
|client_IP|字符串|ClientIP|字符串|
|client_Model|字符串|ClientModel|字符串|
|client_OS|字符串|ClientOS|字符串|
|client_StateOrProvince|字符串|ClientStateOrProvince|字符串|
|client_type|字符串|ClientType|字符串|
|cloud_RoleInstance|字符串|AppRoleInstance|字符串|
|cloud_RoleName|字符串|AppRoleName|字符串|
|customDimensions|动态|属性|动态|
|customMeasurements|动态|度量|动态|
|duration|real|DurationMs|real|
|`id`|字符串|`Id`|string|
|iKey|字符串|IKey|字符串|
|itemCount|int|ItemCount|int|
|itemId|字符串|\_ItemId|字符串|
|itemType|字符串|类型|字符串|
|name|字符串|名称|字符串|
|operation_Id|字符串|OperationId|字符串|
|operation_Name|字符串|OperationName|字符串|
|operation_ParentId|字符串|OperationParentId|字符串|
|operation_SyntheticSource|字符串|OperationSyntheticSource|字符串|
|performanceBucket|字符串|PerformanceBucket|字符串|
|sdkVersion|字符串|SdkVersion|字符串|
|session_Id|字符串|SessionId|字符串|
|timestamp|datetime|TimeGenerated|datetime|
|url|字符串|Url|字符串|
|user_AccountId|字符串|UserAccountId|字符串|
|user_AuthenticatedId|字符串|UserAuthenticatedId|字符串|
|user_Id|字符串|UserId|字符串|

### <a name="appperformancecounters"></a>AppPerformanceCounters

旧表： performanceCounters

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|字符串|\_ResourceGUID|字符串|
|application_Version|字符串|AppVersion|字符串|
|appName|字符串|\_ResourceId|字符串|
|category|字符串|类别|字符串|
|client_Browser|字符串|ClientBrowser|字符串|
|client_City|字符串|ClientCity|字符串|
|client_CountryOrRegion|字符串|ClientCountryOrRegion|字符串|
|client_IP|字符串|ClientIP|字符串|
|client_Model|字符串|ClientModel|字符串|
|client_OS|字符串|ClientOS|字符串|
|client_StateOrProvince|字符串|ClientStateOrProvince|字符串|
|client_type|字符串|ClientType|字符串|
|cloud_RoleInstance|字符串|AppRoleInstance|字符串|
|cloud_RoleName|字符串|AppRoleName|字符串|
|counter|字符串|删除 () ||
|customDimensions|动态|属性|动态|
|iKey|字符串|IKey|字符串|
|instance|字符串|实例|字符串|
|itemId|字符串|\_ItemId|字符串|
|itemType|字符串|类型|字符串|
|name|字符串|名称|字符串|
|operation_Id|字符串|OperationId|字符串|
|operation_Name|字符串|OperationName|字符串|
|operation_ParentId|字符串|OperationParentId|字符串|
|operation_SyntheticSource|字符串|OperationSyntheticSource|字符串|
|sdkVersion|字符串|SdkVersion|字符串|
|session_Id|字符串|SessionId|字符串|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|字符串|UserAccountId|字符串|
|user_AuthenticatedId|字符串|UserAuthenticatedId|字符串|
|user_Id|字符串|UserId|字符串|
|value|real|值|real|

### <a name="apprequests"></a>AppRequests

旧表：请求

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|字符串|\_ResourceGUID|字符串|
|application_Version|字符串|AppVersion|字符串|
|appName|字符串|\_ResourceId|字符串|
|client_Browser|字符串|ClientBrowser|字符串|
|client_City|字符串|ClientCity|字符串|
|client_CountryOrRegion|字符串|ClientCountryOrRegion|字符串|
|client_IP|字符串|ClientIP|字符串|
|client_Model|字符串|ClientModel|字符串|
|client_OS|字符串|ClientOS|字符串|
|client_StateOrProvince|字符串|ClientStateOrProvince|字符串|
|client_type|字符串|ClientType|字符串|
|cloud_RoleInstance|字符串|AppRoleInstance|字符串|
|cloud_RoleName|字符串|AppRoleName|字符串|
|customDimensions|动态|属性|动态|
|customMeasurements|动态|度量|动态|
|duration|real|DurationMs|Real|
|`id`|字符串|`Id`|字符串|
|iKey|字符串|IKey|字符串|
|itemCount|int|ItemCount|int|
|itemId|字符串|\_ItemId|字符串|
|itemType|字符串|类型|字符串|
|name|字符串|名称|String|
|operation_Id|字符串|OperationId|字符串|
|operation_Name|字符串|OperationName|字符串|
|operation_ParentId|字符串|OperationParentId|字符串|
|operation_SyntheticSource|字符串|OperationSyntheticSource|字符串|
|performanceBucket|字符串|PerformanceBucket|字符串|
|resultCode|string|ResultCode|字符串|
|sdkVersion|字符串|SdkVersion|字符串|
|session_Id|字符串|SessionId|字符串|
|source|字符串|源|字符串|
|success|字符串|成功|Bool|
|timestamp|datetime|TimeGenerated|datetime|
|url|字符串|Url|字符串|
|user_AccountId|字符串|UserAccountId|字符串|
|user_AuthenticatedId|字符串|UserAuthenticatedId|字符串|
|user_Id|字符串|UserId|字符串|

### <a name="appsystemevents"></a>AppSystemEvents

旧表：异常

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|字符串|\_ResourceGUID|字符串|
|application_Version|字符串|AppVersion|字符串|
|appName|字符串|\_ResourceId|字符串|
|程序集 (assembly)|字符串|程序集|字符串|
|client_Browser|字符串|ClientBrowser|字符串|
|client_City|字符串|ClientCity|字符串|
|client_CountryOrRegion|字符串|ClientCountryOrRegion|字符串|
|client_IP|字符串|ClientIP|字符串|
|client_Model|字符串|ClientModel|字符串|
|client_OS|字符串|ClientOS|字符串|
|client_StateOrProvince|字符串|ClientStateOrProvince|字符串|
|client_type|字符串|ClientType|字符串|
|cloud_RoleInstance|字符串|AppRoleInstance|字符串|
|cloud_RoleName|字符串|AppRoleName|字符串|
|customDimensions|动态|属性|动态|
|customMeasurements|动态|度量|动态|
|详细信息|动态|详细信息|动态|
|handledAt|字符串|HandledAt|字符串|
|iKey|字符串|IKey|字符串|
|innermostAssembly|字符串|InnermostAssembly|字符串|
|innermostMessage|字符串|InnermostMessage|字符串|
|innermostMethod|字符串|InnermostMethod|字符串|
|innermostType|字符串|InnermostType|字符串|
|itemCount|int|ItemCount|int|
|itemId|字符串|\_ItemId|字符串|
|itemType|字符串|类型|字符串|
|message|字符串|Message|字符串|
|method|字符串|方法|字符串|
|operation_Id|字符串|OperationId|字符串|
|operation_Name|字符串|OperationName|字符串|
|operation_ParentId|字符串|OperationParentId|字符串|
|operation_SyntheticSource|字符串|OperationSyntheticSource|字符串|
|outerAssembly|字符串|OuterAssembly|字符串|
|outerMessage|字符串|OuterMessage|字符串|
|outerMethod|字符串|OuterMethod|字符串|
|outerType|字符串|OuterType|字符串|
|problemId|字符串|ProblemId|字符串|
|sdkVersion|字符串|SdkVersion|字符串|
|session_Id|字符串|SessionId|字符串|
|severityLevel|int|SeverityLevel|int|
|timestamp|datetime|TimeGenerated|datetime|
|type|字符串|ExceptionType|字符串|
|user_AccountId|字符串|UserAccountId|字符串|
|user_AuthenticatedId|字符串|UserAuthenticatedId|字符串|
|user_Id|字符串|UserId|字符串|

### <a name="apptraces"></a>AppTraces

旧表：跟踪

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|字符串|\_ResourceGUID|字符串|
|application_Version|字符串|AppVersion|字符串|
|appName|字符串|\_ResourceId|字符串|
|client_Browser|字符串|ClientBrowser|字符串|
|client_City|字符串|ClientCity|字符串|
|client_CountryOrRegion|字符串|ClientCountryOrRegion|字符串|
|client_IP|字符串|ClientIP|字符串|
|client_Model|字符串|ClientModel|字符串|
|client_OS|字符串|ClientOS|字符串|
|client_StateOrProvince|字符串|ClientStateOrProvince|字符串|
|client_type|字符串|ClientType|字符串|
|cloud_RoleInstance|字符串|AppRoleInstance|字符串|
|cloud_RoleName|字符串|AppRoleName|字符串|
|customDimensions|动态|属性|动态|
|customMeasurements|动态|度量|动态|
|iKey|字符串|IKey|字符串|
|itemCount|int|ItemCount|int|
|itemId|字符串|\_ItemId|字符串|
|itemType|字符串|类型|字符串|
|message|字符串|Message|字符串|
|operation_Id|字符串|OperationId|字符串|
|operation_Name|字符串|OperationName|字符串|
|operation_ParentId|字符串|OperationParentId|字符串|
|operation_SyntheticSource|字符串|OperationSyntheticSource|字符串|
|sdkVersion|字符串|SdkVersion|字符串|
|session_Id|字符串|SessionId|字符串|
|severityLevel|int|SeverityLevel|int|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|字符串|UserAccountId|字符串|
|user_AuthenticatedId|字符串|UserAuthenticatedId|字符串|
|user_Id|字符串|UserId|string|

## <a name="next-steps"></a>后续步骤

* [探索指标](../platform/metrics-charts.md)
* [编写分析查询](../log-query/log-query-overview.md)


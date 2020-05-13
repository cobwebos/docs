---
title: 基于工作区的资源架构 Azure Monitor Application Insights
description: 了解 Azure Monitor Application Insights 基于工作区的资源的新表结构和架构。
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/09/2020
ms.openlocfilehash: 21f387a87224615ea6afbdce620c56e3ad2cc6ea
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210536"
---
# <a name="workspace-based-resource-changes-preview"></a>基于工作区的资源更改（预览）

在引入[基于工作区的 Application Insights 资源](create-workspace-resource.md)之前，Application Insights 数据与 Azure Monitor 中的其他日志数据单独存储。 两者都基于 Azure 数据资源管理器，并使用相同的 Kusto 查询语言（KQL）。 Azure Monitor 中的[日志](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)中对此进行了说明。

使用基于工作区的 Application Insights 资源数据存储在包含其他监视数据和应用程序数据的 Log Analytics 工作区中。 这可以简化配置，使你能够更轻松地跨多个解决方案分析数据并利用工作区的功能。

## <a name="table-structure"></a>表结构

| 旧表名称 | 新表名称 | 说明 |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  可用性测试中的摘要数据。|
| browserTimings | AppBrowserTimings | 有关客户端性能的数据，例如处理传入数据所用的时间。|
| dependencies | AppDependencies | 从应用程序调用到通过 TrackDependency （）记录的其他组件（包括外部组件）-例如，对 REST API、数据库或文件系统的调用。  |
| customEvents | AppEvents | 应用程序创建的自定义事件。 |
| customMetrics | AppMetrics | 应用程序创建的自定义指标。 |
| pageViews | AppPageViews| 每个网站的浏览情况数据，以及浏览器信息。 |
| performanceCounters | AppPerformanceCounters | 支持应用程序的计算资源的性能度量，例如 Windows 性能计数器。 |
| 请求 | AppRequests | 应用程序收到的请求。 例如，将为 web 应用接收的每个 HTTP 请求记录一个单独的请求记录。  |
| exceptions | AppSystemEvents | 应用程序运行时引发的异常捕获服务器端和客户端（浏览器）异常。 |
| traces | AppTraces | 通过 TrackTrace （）记录的应用程序代码/日志记录框架发出的详细日志（跟踪）。 |

## <a name="table-schemas"></a>表架构

以下部分显示了经典属性名称和基于新工作区的 Application Insights 属性名称之间的映射。  使用此信息可转换使用旧表的任何查询。

大多数列具有相同的名称，并且具有不同的大小写。 由于 KQL 区分大小写，因此需要更改每个列名以及现有查询中的表名。 突出显示除了大写之外的更改的列。 你仍可以在 Application Insights 资源的 "**日志**" 窗格内使用经典 Application Insights 查询，即使它是基于工作区的资源。 当从 Log Analytics 工作区体验的上下文中进行查询时，必须提供新的属性名。

### <a name="appavailabilityresults"></a>AppAvailabilityResults

旧表：可用性

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|动态|“属性”|动态|
|customMeasurements|动态|度量|动态|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|类型|String|
|位置|string|位置|string|
|message|string|消息|string|
|name|string|名称|字符串|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|大小|real|大小|real|
|success|string|Success|Bool|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appbrowsertimings"></a>AppBrowserTimings

旧表： browserTimings

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|动态|“属性”|动态|
|customMeasurements|动态|度量|动态|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|类型|string|
|name|string|名称|datetime|
|networkDuration|real|NetworkDurationMs|real|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|processingDuration|real|ProcessingDurationMs|real|
|receiveDuration|real|ReceiveDurationMs|real|
|sdkVersion|string|SdkVersion|string|
|sendDuration|real|SendDurationMs|real|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|totalDuration|real|TotalDurationMs|real|
|url|string|URL|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appdependencies"></a>AppDependencies

旧表：依赖关系

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|动态|“属性”|动态|
|customMeasurements|动态|度量|动态|
|data|string|数据|string|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|类型|String|
|name|string|名称|字符串|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|resultCode|string|ResultCode|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|success|string|Success|Bool|
|目标|string|目标|string|
|timestamp|datetime|TimeGenerated|datetime|
|type|string|DependencyType|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appevents"></a>AppEvents

旧表： customEvents

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|动态|“属性”|动态|
|customMeasurements|动态|度量|动态|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|类型|string|
|name|string|名称|字符串|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appmetrics"></a>AppMetrics

旧表： customMetrics

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|动态|“属性”|动态|
|iKey|string|IKey|string|
|itemId|string|\_ItemId|string|
|itemType|string|类型|string|
|name|string|名称|字符串|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|
|value|real|删除||
|valueCount|int|ValueCount|int|
|valueMax|real|ValueMax|real|
|valueMin|real|ValueMin|real|
|valueStdDev|real|ValueStdDev|real|
|valueSum|real|ValueSum|real|

### <a name="apppageviews"></a>AppPageViews

旧表： pageViews

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|动态|“属性”|动态|
|customMeasurements|动态|度量|动态|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|类型|String|
|name|string|名称|字符串|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|url|string|URL|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appperformancecounters"></a>AppPerformanceCounters

旧表： performanceCounters

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|category|string|类别|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|counter|string|删除||
|customDimensions|动态|“属性”|动态|
|iKey|string|IKey|string|
|instance|string|实例|string|
|itemId|string|\_ItemId|string|
|itemType|string|类型|string|
|name|string|名称|字符串|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|
|value|real|值|real|

### <a name="apprequests"></a>AppRequests

旧表：请求

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|动态|“属性”|动态|
|customMeasurements|动态|度量|动态|
|duration|real|DurationMs|Real|
|`id`|string|`Id`|String|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|类型|String|
|name|string|名称|String|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|String|
|resultCode|string|ResultCode|String|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|源|string|Source|String|
|success|string|Success|Bool|
|timestamp|datetime|TimeGenerated|datetime|
|url|string|URL|String|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appsystemevents"></a>AppSystemEvents

旧表：异常

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|程序集 (assembly)|string|Assembly|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|动态|“属性”|动态|
|customMeasurements|动态|度量|动态|
|详细信息|动态|详细信息|动态|
|handledAt|string|HandledAt|string|
|iKey|string|IKey|string|
|innermostAssembly|string|InnermostAssembly|string|
|innermostMessage|string|InnermostMessage|string|
|innermostMethod|string|InnermostMethod|string|
|innermostType|string|InnermostType|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|类型|string|
|message|string|消息|string|
|method|string|方法|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|outerAssembly|string|OuterAssembly|string|
|outerMessage|string|OuterMessage|string|
|outerMethod|string|OuterMethod|string|
|outerType|string|OuterType|string|
|problemId|string|ProblemId|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|severityLevel|int|SeverityLevel|int|
|timestamp|datetime|TimeGenerated|datetime|
|type|字符串|ExceptionType|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="apptraces"></a>AppTraces

旧表：跟踪

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|动态|“属性”|动态|
|customMeasurements|动态|度量|动态|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|类型|字符串|
|message|string|消息|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|severityLevel|int|SeverityLevel|int|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

## <a name="next-steps"></a>后续步骤

* [探索指标](../../azure-monitor/platform/metrics-charts.md)
* [编写分析查询](../../azure-monitor/app/analytics.md)
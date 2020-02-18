---
title: Azure Monitor 的常见警报架构定义
description: 了解 Azure Monitor 的常见警报架构定义
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: ofirmanor
ms.author: robb
ms.date: 03/14/2019
ms.openlocfilehash: efa6a8807b3db14649a2b4ad38c575cf98aba113
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425164"
---
# <a name="common-alert-schema-definitions"></a>常见警报架构定义

本文介绍 Azure Monitor 的[常见警报架构定义](https://aka.ms/commonAlertSchemaDocs)，包括 Webhook、Azure 逻辑应用、Azure Functions 和 Azure 自动化 runbook 的架构定义。 

任何警报实例都描述受影响的资源和警报的原因。 以下部分中的常见架构介绍了这些实例：
* **Essentials**：一组标准化字段，这些字段在所有警报类型中通用，它们描述了警报所处的资源以及其他常见的警报元数据（例如严重性或说明）。 
* **警报上下文**：描述警报原因的一组字段，其中的字段根据警报类型而变化。 例如，指标警报在警报上下文中包含诸如 "指标名称" 和 "指标" 值之类的字段，而活动日志警报包含有关生成警报的事件的信息。 

**示例警报负载**
```json
{
  "schemaId": "azureMonitorCommonAlertSchema",
  "data": {
    "essentials": {
      "alertId": "/subscriptions/<subscription ID>/providers/Microsoft.AlertsManagement/alerts/b9569717-bc32-442f-add5-83a997729330",
      "alertRule": "WCUS-R2-Gen2",
      "severity": "Sev3",
      "signalType": "Metric",
      "monitorCondition": "Resolved",
      "monitoringService": "Platform",
      "alertTargetIDs": [
        "/subscriptions/<subscription ID>/resourcegroups/pipelinealertrg/providers/microsoft.compute/virtualmachines/wcus-r2-gen2"
      ],
      "originAlertId": "3f2d4487-b0fc-4125-8bd5-7ad17384221e_PipeLineAlertRG_microsoft.insights_metricAlerts_WCUS-R2-Gen2_-117781227",
      "firedDateTime": "2019-03-22T13:58:24.3713213Z",
      "resolvedDateTime": "2019-03-22T14:03:16.2246313Z",
      "description": "",
      "essentialsVersion": "1.0",
      "alertContextVersion": "1.0"
    },
    "alertContext": {
      "properties": null,
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Percentage CPU",
            "metricNamespace": "Microsoft.Compute/virtualMachines",
            "operator": "GreaterThan",
            "threshold": "25",
            "timeAggregation": "Average",
            "dimensions": [
              {
                "name": "ResourceId",
                "value": "3efad9dc-3d50-4eac-9c87-8b3fd6f97e4e"
              }
            ],
            "metricValue": 7.727
          }
        ]
      }
    }
  }
}
```

## <a name="essentials"></a>Essentials

| 字段 | 说明|
|:---|:---|
| alertId | 用于唯一标识警报实例的 GUID。 |
| alertRule | 生成警报实例的警报规则的名称。 |
| Severity | 警报的严重性。 可能的值： Sev0、Sev1、Sev2、Sev3 或 Sev4。 |
| signalType | 标识在其上定义警报规则的信号。 可能的值： "指标"、"日志" 或 "活动日志"。 |
| monitorCondition | 当警报触发时，警报的监视条件将被设置为 "已**触发**"。 当导致触发警报的基础条件清除时，监视条件设置为 "**已解决**"。   |
| monitoringService | 生成警报的监视服务或解决方案。 警报上下文的字段由监视服务决定。 |
| alertTargetIds | 受警报目标的 Azure 资源管理器 Id 的列表。 对于在 Log Analytics 工作区或 Application Insights 实例上定义的日志警报，该警报是各自的工作区或应用程序。 |
| originAlertId | 警报实例的 ID，该 ID 由生成它的监视服务生成。 |
| firedDateTime | 以协调世界时（UTC）触发警报实例的日期和时间。 |
| resolvedDateTime | 警报实例的监视器条件设置为在 UTC 中**解决**的日期和时间。 目前仅适用于指标警报。|
| description | 警报规则中定义的说明。 |
|essentialsVersion| Essentials 部分的版本号。|
|alertContextVersion | `alertContext` 部分的版本号。 |

**示例值**
```json
{
  "essentials": {
    "alertId": "/subscriptions/<subscription ID>/providers/Microsoft.AlertsManagement/alerts/b9569717-bc32-442f-add5-83a997729330",
    "alertRule": "Contoso IT Metric Alert",
    "severity": "Sev3",
    "signalType": "Metric",
    "monitorCondition": "Fired",
    "monitoringService": "Platform",
    "alertTargetIDs": [
      "/subscriptions/<subscription ID>/resourceGroups/aimon-rg/providers/Microsoft.Insights/components/ai-orion-int-fe"
    ],
    "originAlertId": "74ff8faa0c79db6084969cf7c72b0710e51aec70b4f332c719ab5307227a984f",
    "firedDateTime": "2019-03-26T05:25:50.4994863Z",
    "description": "Test Metric alert",
    "essentialsVersion": "1.0",
    "alertContextVersion": "1.0"
  }
}
```

## <a name="alert-context"></a>警报上下文

### <a name="metric-alerts"></a>指标警报

#### <a name="monitoringservice--platform"></a>`monitoringService` = `Platform`

**示例值**
```json
{
  "alertContext": {
      "properties": null,
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Percentage CPU",
            "metricNamespace": "Microsoft.Compute/virtualMachines",
            "operator": "GreaterThan",
            "threshold": "25",
            "timeAggregation": "Average",
            "dimensions": [
              {
                "name": "ResourceId",
                "value": "3efad9dc-3d50-4eac-9c87-8b3fd6f97e4e"
              }
            ],
            "metricValue": 31.1105
          }
        ],
        "windowStartTime": "2019-03-22T13:40:03.064Z",
        "windowEndTime": "2019-03-22T13:45:03.064Z"
      }
    }
}
```

### <a name="log-alerts"></a>日志警报

> [!NOTE]
> 对于定义了自定义电子邮件主题和/或 JSON 有效负载的日志警报，启用通用架构会将电子邮件主题和/或负载架构恢复为如下所述的架构。 对于启用了通用架构的警报，每个警报的大小上限为 256 KB。 如果搜索结果导致警报大小超过此阈值，则不会将其嵌入日志警报有效负载。 可以通过检查标志 `IncludeSearchResults`来确定这一点。 如果不包含搜索结果，则应将搜索查询与[LOG ANALYTICS API](https://docs.microsoft.com/rest/api/loganalytics/query/get)一起使用。 

#### <a name="monitoringservice--log-analytics"></a>`monitoringService` = `Log Analytics`

**示例值**
```json
{
  "alertContext": {
    "SearchQuery": "search * \n| where Type == \"Heartbeat\" \n| where Category == \"Direct Agent\" \n| where TimeGenerated > ago(30m) ",
    "SearchIntervalStartTimeUtc": "3/22/2019 1:36:31 PM",
    "SearchIntervalEndtimeUtc": "3/22/2019 1:51:31 PM",
    "ResultCount": 15,
    "LinkToSearchResults": "https://portal.azure.com#@72f988bf-86f1-41af-91ab-2d7cd011db47/blade/Microsoft_OperationsManagementSuite_Workspace/AnalyticsBlade/initiator/AnalyticsShareLinkToQuery/isQueryEditorVisible/true/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%<subscription ID>%2FresourceGroups%2Fpipelinealertrg%2Fproviders%2FMicrosoft.OperationalInsights%2Fworkspaces%2FINC-OmsAlertRunner%22%7D%5D%7D/query/search%20%2A%20%0A%7C%20where%20Type%20%3D%3D%20%22Heartbeat%22%20%0A%7C%20where%20Category%20%3D%3D%20%22Direct%20Agent%22%20%0A%7C%20where%20TimeGenerated%20%3E%20%28datetime%282019-03-22T13%3A51%3A31.0000000%29%20-%2030m%29%20%20/isQuerybase64Compressed/false/timespanInIsoFormat/2019-03-22T13%3a36%3a31.0000000Z%2f2019-03-22T13%3a51%3a31.0000000Z",
    "SeverityDescription": "Warning",
    "WorkspaceId": "2a1f50a7-ef97-420c-9d14-938e77c2a929",
    "SearchIntervalDurationMin": "15",
    "AffectedConfigurationItems": [
      "INC-Gen2Alert"
    ],
    "SearchIntervalInMinutes": "15",
    "Threshold": 10000,
    "Operator": "Less Than",
    "SearchResult": {
      "tables": [
        {
          "name": "PrimaryResult",
          "columns": [
            {
              "name": "$table",
              "type": "string"
            },
            {
              "name": "Id",
              "type": "string"
            },
            {
              "name": "TimeGenerated",
              "type": "datetime"
            }
          ],
          "rows": [
            [
              "Fabrikam",
              "33446677a",
              "2018-02-02T15:03:12.18Z"
            ],
            [
              "Contoso",
              "33445566b",
              "2018-02-02T15:16:53.932Z"
            ]
          ]
        }
      ],
      "dataSources": [
        {
          "resourceId": "/subscriptions/a5ea27e2-7482-49ba-90b3-60e7496dd873/resourcegroups/nrt-tip-kc/providers/microsoft.operationalinsights/workspaces/nrt-tip-kc",
          "tables": [
            "Heartbeat"
          ]
        }
      ]
    },
    "IncludeSearchResults": "True",
    "AlertType": "Number of results"
  }
}
```

#### <a name="monitoringservice--application-insights"></a>`monitoringService` = `Application Insights`

**示例值**
```json
{
  "alertContext": {
    "SearchQuery": "search *",
    "SearchIntervalStartTimeUtc": "3/22/2019 1:36:33 PM",
    "SearchIntervalEndtimeUtc": "3/22/2019 1:51:33 PM",
    "ResultCount": 0,
    "LinkToSearchResults": "https://portal.azure.com#@72f988bf-86f1-41af-91ab-2d7cd011db47/blade/Microsoft_OperationsManagementSuite_Workspace/AnalyticsBlade/initiator/AnalyticsShareLinkToQuery/isQueryEditorVisible/true/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%<subscription ID>%2FresourceGroups%2FPipeLineAlertRG%2Fproviders%2Fmicrosoft.insights%2Fcomponents%2FWEU-AIRunner%22%7D%5D%7D/query/search%20%2A/isQuerybase64Compressed/false/timespanInIsoFormat/2019-03-22T13%3a36%3a33.0000000Z%2f2019-03-22T13%3a51%3a33.0000000Z",
    "SearchIntervalDurationMin": "15",
    "SearchIntervalInMinutes": "15",
    "Threshold": 10000,
    "Operator": "Less Than",
    "ApplicationId": "8e20151d-75b2-4d66-b965-153fb69d65a6",
    "SearchResult": {
      "tables": [
        {
          "name": "PrimaryResult",
          "columns": [
            {
              "name": "$table",
              "type": "string"
            },
            {
              "name": "Id",
              "type": "string"
            },
            {
              "name": "TimeGenerated",
              "type": "datetime"
            }
          ],
          "rows": [
            [
              "Fabrikam",
              "33446677a",
              "2018-02-02T15:03:12.18Z"
            ],
            [
              "Contoso",
              "33445566b",
              "2018-02-02T15:16:53.932Z"
            ]
          ]
        }
      ],
      "dataSources": [
        {
          "resourceId": "/subscriptions/a5ea27e2-7482-49ba-90b3-60e7496dd873/resourcegroups/nrt-tip-kc/providers/microsoft.operationalinsights/workspaces/nrt-tip-kc",
          "tables": [
            "Heartbeat"
          ]
        }
      ]
    },
    "IncludeSearchResults": "True",
    "AlertType": "Number of results"
  }
}
```

### <a name="activity-log-alerts"></a>活动日志警报

#### <a name="monitoringservice--activity-log---administrative"></a>`monitoringService` = `Activity Log - Administrative`

**示例值**
```json
{
  "alertContext": {
      "authorization": {
        "action": "Microsoft.Compute/virtualMachines/restart/action",
        "scope": "/subscriptions/<subscription ID>/resourceGroups/PipeLineAlertRG/providers/Microsoft.Compute/virtualMachines/WCUS-R2-ActLog"
      },
      "channels": "Operation",
      "claims": "{\"aud\":\"https://management.core.windows.net/\",\"iss\":\"https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/\",\"iat\":\"1553260826\",\"nbf\":\"1553260826\",\"exp\":\"1553264726\",\"aio\":\"42JgYNjdt+rr+3j/dx68v018XhuFAwA=\",\"appid\":\"e9a02282-074f-45cf-93b0-50568e0e7e50\",\"appidacr\":\"2\",\"http://schemas.microsoft.com/identity/claims/identityprovider\":\"https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/\",\"http://schemas.microsoft.com/identity/claims/objectidentifier\":\"9778283b-b94c-4ac6-8a41-d5b493d03aa3\",\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\":\"9778283b-b94c-4ac6-8a41-d5b493d03aa3\",\"http://schemas.microsoft.com/identity/claims/tenantid\":\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\"uti\":\"v5wYC9t9ekuA2rkZSVZbAA\",\"ver\":\"1.0\"}",
      "caller": "9778283b-b94c-4ac6-8a41-d5b493d03aa3",
      "correlationId": "8ee9c32a-92a1-4a8f-989c-b0ba09292a91",
      "eventSource": "Administrative",
      "eventTimestamp": "2019-03-22T13:56:31.2917159+00:00",
      "eventDataId": "161fda7e-1cb4-4bc5-9c90-857c55a8f57b",
      "level": "Informational",
      "operationName": "Microsoft.Compute/virtualMachines/restart/action",
      "operationId": "310db69b-690f-436b-b740-6103ab6b0cba",
      "status": "Succeeded",
      "subStatus": "",
      "submissionTimestamp": "2019-03-22T13:56:54.067593+00:00"
    }
}
```

#### <a name="monitoringservice--activity-log---policy"></a>`monitoringService` = `Activity Log - Policy`

**示例值**
```json
{
  "alertContext": {
    "authorization": {
      "action": "Microsoft.Resources/checkPolicyCompliance/read",
      "scope": "/subscriptions/<GUID>"
    },
    "channels": "Operation",
    "claims": "{\"aud\":\"https://management.azure.com/\",\"iss\":\"https://sts.windows.net/<GUID>/\",\"iat\":\"1566711059\",\"nbf\":\"1566711059\",\"exp\":\"1566740159\",\"aio\":\"42FgYOhynHNw0scy3T/bL71+xLyqEwA=\",\"appid\":\"<GUID>\",\"appidacr\":\"2\",\"http://schemas.microsoft.com/identity/claims/identityprovider\":\"https://sts.windows.net/<GUID>/\",\"http://schemas.microsoft.com/identity/claims/objectidentifier\":\"<GUID>\",\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\":\"<GUID>\",\"http://schemas.microsoft.com/identity/claims/tenantid\":\"<GUID>\",\"uti\":\"Miy1GzoAG0Scu_l3m1aIAA\",\"ver\":\"1.0\"}",
    "caller": "<GUID>",
    "correlationId": "<GUID>",
    "eventSource": "Policy",
    "eventTimestamp": "2019-08-25T11:11:34.2269098+00:00",
    "eventDataId": "<GUID>",
    "level": "Warning",
    "operationName": "Microsoft.Authorization/policies/audit/action",
    "operationId": "<GUID>",
    "properties": {
      "isComplianceCheck": "True",
      "resourceLocation": "eastus2",
      "ancestors": "<GUID>",
      "policies": "[{\"policyDefinitionId\":\"/providers/Microsoft.Authorization/policyDefinitions/<GUID>/\",\"policySetDefinitionId\":\"/providers/Microsoft.Authorization/policySetDefinitions/<GUID>/\",\"policyDefinitionReferenceId\":\"vulnerabilityAssessmentMonitoring\",\"policySetDefinitionName\":\"<GUID>\",\"policyDefinitionName\":\"<GUID>\",\"policyDefinitionEffect\":\"AuditIfNotExists\",\"policyAssignmentId\":\"/subscriptions/<GUID>/providers/Microsoft.Authorization/policyAssignments/SecurityCenterBuiltIn/\",\"policyAssignmentName\":\"SecurityCenterBuiltIn\",\"policyAssignmentScope\":\"/subscriptions/<GUID>\",\"policyAssignmentSku\":{\"name\":\"A1\",\"tier\":\"Standard\"},\"policyAssignmentParameters\":{}}]"
    },
    "status": "Succeeded",
    "subStatus": "",
    "submissionTimestamp": "2019-08-25T11:12:46.1557298+00:00"
  }
}
```

#### <a name="monitoringservice--activity-log---autoscale"></a>`monitoringService` = `Activity Log - Autoscale`

**示例值**
```json
{
  "alertContext": {
    "channels": "Admin, Operation",
    "claims": "{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn\":\"Microsoft.Insights/autoscaleSettings\"}",
    "caller": "Microsoft.Insights/autoscaleSettings",
    "correlationId": "<GUID>",
    "eventSource": "Autoscale",
    "eventTimestamp": "2019-08-21T16:17:47.1551167+00:00",
    "eventDataId": "<GUID>",
    "level": "Informational",
    "operationName": "Microsoft.Insights/AutoscaleSettings/Scaleup/Action",
    "operationId": "<GUID>",
    "properties": {
      "description": "The autoscale engine attempting to scale resource '/subscriptions/d<GUID>/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachineScaleSets/testVMSS' from 9 instances count to 10 instances count.",
      "resourceName": "/subscriptions/<GUID>/resourceGroups/voiceassistancedemo/providers/Microsoft.Compute/virtualMachineScaleSets/alexademo",
      "oldInstancesCount": "9",
      "newInstancesCount": "10",
      "activeAutoscaleProfile": "{\r\n  \"Name\": \"Auto created scale condition\",\r\n  \"Capacity\": {\r\n    \"Minimum\": \"1\",\r\n    \"Maximum\": \"10\",\r\n    \"Default\": \"1\"\r\n  },\r\n  \"Rules\": [\r\n    {\r\n      \"MetricTrigger\": {\r\n        \"Name\": \"Percentage CPU\",\r\n        \"Namespace\": \"microsoft.compute/virtualmachinescalesets\",\r\n        \"Resource\": \"/subscriptions/<GUID>/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachineScaleSets/testVMSS\",\r\n        \"ResourceLocation\": \"eastus\",\r\n        \"TimeGrain\": \"PT1M\",\r\n        \"Statistic\": \"Average\",\r\n        \"TimeWindow\": \"PT5M\",\r\n        \"TimeAggregation\": \"Average\",\r\n        \"Operator\": \"GreaterThan\",\r\n        \"Threshold\": 0.0,\r\n        \"Source\": \"/subscriptions/<GUID>/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachineScaleSets/testVMSS\",\r\n        \"MetricType\": \"MDM\",\r\n        \"Dimensions\": [],\r\n        \"DividePerInstance\": false\r\n      },\r\n      \"ScaleAction\": {\r\n        \"Direction\": \"Increase\",\r\n        \"Type\": \"ChangeCount\",\r\n        \"Value\": \"1\",\r\n        \"Cooldown\": \"PT1M\"\r\n      }\r\n    }\r\n  ]\r\n}",
      "lastScaleActionTime": "Wed, 21 Aug 2019 16:17:47 GMT"
    },
    "status": "Succeeded",
    "submissionTimestamp": "2019-08-21T16:17:47.2410185+00:00"
  }
}
```

#### <a name="monitoringservice--activity-log---security"></a>`monitoringService` = `Activity Log - Security`

**示例值**
```json
{
  "alertContext": {
    "channels": "Operation",
    "correlationId": "<GUID>",
    "eventSource": "Security",
    "eventTimestamp": "2019-08-26T08:34:14+00:00",
    "eventDataId": "<GUID>",
    "level": "Informational",
    "operationName": "Microsoft.Security/locations/alerts/activate/action",
    "operationId": "<GUID>",
    "properties": {
      "threatStatus": "Quarantined",
      "category": "Virus",
      "threatID": "2147519003",
      "filePath": "C:\\AlertGeneration\\test.eicar",
      "protectionType": "Windows Defender",
      "actionTaken": "Blocked",
      "resourceType": "Virtual Machine",
      "severity": "Low",
      "compromisedEntity": "testVM",
      "remediationSteps": "[\"No user action is necessary\"]",
      "attackedResourceType": "Virtual Machine"
    },
    "status": "Active",
    "submissionTimestamp": "2019-08-26T09:28:58.3019107+00:00"
  }
}
```

#### <a name="monitoringservice--servicehealth"></a>`monitoringService` = `ServiceHealth`

**示例值**
```json
{
  "alertContext": {
    "authorization": null,
    "channels": 1,
    "claims": null,
    "caller": null,
    "correlationId": "f3cf2430-1ee3-4158-8e35-7a1d615acfc7",
    "eventSource": 2,
    "eventTimestamp": "2019-06-24T11:31:19.0312699+00:00",
    "httpRequest": null,
    "eventDataId": "<GUID>",
    "level": 3,
    "operationName": "Microsoft.ServiceHealth/maintenance/action",
    "operationId": "<GUID>",
    "properties": {
      "title": "Azure SQL DW Scheduled Maintenance Pending",
      "service": "SQL Data Warehouse",
      "region": "East US",
      "communication": "<MESSAGE>",
      "incidentType": "Maintenance",
      "trackingId": "<GUID>",
      "impactStartTime": "2019-06-26T04:00:00Z",
      "impactMitigationTime": "2019-06-26T12:00:00Z",
      "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"East US\"}],\"ServiceName\":\"SQL Data Warehouse\"}]",
      "impactedServicesTableRows": "<tr>\r\n<td align='center' style='padding: 5px 10px; border-right:1px solid black; border-bottom:1px solid black'>SQL Data Warehouse</td>\r\n<td align='center' style='padding: 5px 10px; border-bottom:1px solid black'>East US<br></td>\r\n</tr>\r\n",
      "defaultLanguageTitle": "Azure SQL DW Scheduled Maintenance Pending",
      "defaultLanguageContent": "<MESSAGE>",
      "stage": "Planned",
      "communicationId": "<GUID>",
      "maintenanceId": "<GUID>",
      "isHIR": "false",
      "version": "0.1.1"
    },
    "status": "Active",
    "subStatus": null,
    "submissionTimestamp": "2019-06-24T11:31:31.7147357+00:00",
    "ResourceType": null
  }
}
```
#### <a name="monitoringservice--resource-health"></a>`monitoringService` = `Resource Health`

**示例值**
```json
{
  "alertContext": {
    "channels": "Admin, Operation",
    "correlationId": "<GUID>",
    "eventSource": "ResourceHealth",
    "eventTimestamp": "2019-06-24T15:42:54.074+00:00",
    "eventDataId": "<GUID>",
    "level": "Informational",
    "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
    "operationId": "<GUID>",
    "properties": {
      "title": "This virtual machine is stopping and deallocating as requested by an authorized user or process",
      "details": null,
      "currentHealthStatus": "Unavailable",
      "previousHealthStatus": "Available",
      "type": "Downtime",
      "cause": "UserInitiated"
    },
    "status": "Active",
    "submissionTimestamp": "2019-06-24T15:45:20.4488186+00:00"
  }
}
```


## <a name="next-steps"></a>后续步骤

- 了解有关[常见警报架构](https://aka.ms/commonAlertSchemaDocs)的详细信息。
- 了解[如何创建一个逻辑应用，该应用使用常见的警报架构来处理所有警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations)。 


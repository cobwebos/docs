---
title: 为新建议创建 Azure 顾问警报 |Microsoft Docs
description: 为新建议创建 Azure 顾问警报
services: advisor
author: sagupt
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/09/2019
ms.author: sagupt
ms.openlocfilehash: a67034752b4c43533a5735b857186ee83934717a
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935633"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>根据新建议创建 Azure 顾问警报 

本文介绍如何使用 Azure 门户和 Azure 资源管理器模板为 Azure Advisor 中的新建议设置警报。 

当 Azure 顾问检测到某个资源的新建议时，会将事件存储在[Azure 活动日志](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview)中。 可以使用特定于建议的警报创建体验，从 Azure Advisor 为这些事件设置警报。 你可以选择一个订阅，还可以选择一个资源组来指定你想要接收警报的资源。 

还可以通过使用以下属性来确定建议类型：

* 类别
* 影响级别
* 建议类型

你还可以配置触发警报时将发生的操作：  

* 选择现有操作组
* 创建新的操作组

若要了解有关操作组的详细信息，请参阅 [创建和管理操作组] （。/azure-monitor/platform/action-groups.md.

> [!NOTE] 
> Advisor 警报目前仅适用于高可用性、性能和成本建议。 不支持安全建议。 

## <a name="in-the-azure-portal"></a>在 Azure 门户中
1. 在**门户**中，选择 " **Azure 顾问**"。

    ![门户中的 Azure 顾问](./media/advisor-alerts/create1.png)

2. 在左侧菜单的 "**监视**" 部分，选择 "**警报**"。 

    ![Advisor 中的警报](./media/advisor-alerts/create2.png)

3. 选择 "**新顾问警报**"。

    ![新顾问警报](./media/advisor-alerts/create3.png)

4. 在 "**作用域**" 部分中，选择要向其发出警报的订阅和资源组（可选）。 

    ![Advisor 警报范围](./media/advisor-alerts/create4.png)

5. 在 "**条件**" 部分中，选择要用于配置警报的方法。 如果要针对某个类别和/或影响级别的所有建议进行警报，请选择 "**类别和影响级别**"。 如果要针对特定类型的所有建议进行警报，请选择 "**建议类型**"。

    ![Azure Advisor 警报条件](./media/advisor-alerts/create5.png)

6. 根据您选择的 "配置方式" 选项，您可以指定条件。 如果你想要所有建议，只需将其余字段留空即可。 

    ![Advisor 警报操作组](./media/advisor-alerts/create6.png)

7. 在 "**操作组**" 部分中，选择 "**添加现有**项" 以使用已创建的操作组，或选择 "**新建**" 以设置新的[操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。 

    ![Advisor 警报添加现有](./media/advisor-alerts/create7.png)

8. 在 "警报详细信息" 部分中，为警报指定名称和简短说明。 如果希望启用警报，请将 "**创建时启用规则**" 选项设置为 **"是"** 。 然后选择要将警报保存到的资源组。 这不会影响建议的目标范围。 

    ![Azure 顾问横幅](./media/advisor-alerts/create8.png)


## <a name="with-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板

此资源管理器模板创建建议警报和新的操作组。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest2",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "name": "[parameters('actionGroups_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      },
      "dependsOn": []
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "name": "[parameters('activityLogAlerts_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
  ```

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>将建议警报配置为使用 webhook
本部分介绍如何配置 Azure Advisor 警报，以便通过 webhook 将建议数据发送到现有系统。 

你可以设置警报，以便在你的某个资源上有新的顾问建议时收到通知。 这些警报可以通过电子邮件或短信通知你，但也可用于通过 webhook 与现有系统集成。 


### <a name="using-the-advisor-recommendation-alert-payload"></a>使用顾问建议警报负载
如果要使用 webhook 将 Advisor 警报集成到自己的系统中，则需要分析从通知发送的 JSON 有效负载。 

为此警报设置操作组时，请选择是否要使用常见的警报架构。 如果选择常见的警报架构，负载将如下所示： 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

如果不使用通用架构，则有效负载如下所示： 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

在任一架构中，你都可以通过查找**eventSource** `Recommendation` is， **operationName**是`Microsoft.Advisor/recommendations/available/action`，来确定顾问建议事件。

你可能想要使用的一些其他重要字段是： 

* *alertTargetIDs*（在公用架构中）或*resourceId* （旧架构）
* *recommendationType*
* *recommendationName*
* *recommendationCategory*
* *recommendationImpact*
* *recommendationResourceLink*


## <a name="manage-your-alerts"></a>管理警报 

在 Azure Advisor 中，可以编辑、删除或禁用和启用建议警报。 

1. 在**门户**中，选择 " **Azure 顾问**"。

    ![Azure 顾问横幅](./media/advisor-alerts/create1.png)

2. 在左侧菜单的 "**监视**" 部分，选择 "**警报**"。

    ![Azure 顾问横幅](./media/advisor-alerts/create2.png)

3. 若要编辑警报，请单击警报名称以打开警报，然后编辑要编辑的字段。

4. 若要删除、启用或禁用警报，请单击行末尾的省略号，并选择要执行的操作。
 


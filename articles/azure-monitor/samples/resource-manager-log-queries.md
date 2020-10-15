---
title: 用于日志查询的资源管理器模板示例
description: 用于部署 Azure Monitor 日志查询的 Azure 资源管理器模板示例。
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: c5c7d1b79f9dd74db8bb8241166b4a0fbc5954c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88855753"
---
# <a name="resource-manager-template-samples-for-log-queries-in-azure-monitor"></a>用于 Azure Monitor 日志查询的资源管理器模板示例
本文包含用于在 Azure Monitor 中创建和配置日志查询的 [Azure 资源管理器模板](../../azure-resource-manager/templates/template-syntax.md)示例。 每个示例都包含模板文件和参数文件，其中包含要提供给模板的示例值。

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="template-references"></a>模板参考

- [Microsoft.OperationalInsights workspaces/savedSearches](/azure/templates/microsoft.operationalinsights/2020-03-01-preview/workspaces/savedsearches)

## <a name="simple-log-query"></a>简单日志查询
以下示例会将日志查询添加到 Log Analytics 工作区。

### <a name="template-file"></a>模板文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string"
      },
      "location": {
        "type": "string"
      }
  },
  "resources": [
    {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "savedSearches",
          "apiVersion": "2015-03-20",
          "name": "VMSS query",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "eTag": "*",
            "displayName": "VMSS Instance Count",
            "category": "VMSS",
            "query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "version": 1
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>参数文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## <a name="log-query-as-a-function"></a>函数形式的日志查询
以下示例会将日志查询作为函数添加到 Log Analytics 工作区。

### <a name="template-file"></a>模板文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string"
      },
      "location": {
        "type": "string"
      }
  },
  "resources": [
    {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2017-03-15-preview",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "savedSearches",
          "apiVersion": "2020-08-01",
          "name": "Cross workspace query",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "properties": {
              "etag": "*",
              "displayName": "Failed Logon Events",
              "category": "Security",
              "FunctionAlias": "failedlogonsecurityevents",
              "query": "
                union withsource=SourceWorkspace
                workspace('workspace1').SecurityEvent,
                workspace('workspace2').SecurityEvent,
                workspace('workspace3').SecurityEvent,
                | where EventID == 4625",
              "version": 1
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>参数文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## <a name="parameterized-function"></a>参数化函数
以下示例会将日志查询作为使用参数的函数添加到 Log Analytics 工作区。 它还会添加第二个使用参数化函数的日志查询。

> [!NOTE]
> 资源模板是目前唯一可用于参数化函数的方法。 一旦将函数安装到工作区中，任何日志查询都可以使用该函数。

### <a name="template-file"></a>模板文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string"
      },
      "location": {
        "type": "string"
      }
  },
  "resources": [
    {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "savedSearches",
          "apiVersion": "2020-08-01",
          "name": "Parameterized function",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "properties": {
              "etag": "*",
              "displayName": "Unavailable computers function",
              "category": "Samples",
              "FunctionAlias": "UnavailableComputers",
              "FunctionParameters": "argSpan: timespan",
              "query": " Heartbeat | summarize LastHeartbeat=max(TimeGenerated) by Computer| where LastHeartbeat < ago(argSpan)"
          }
        },
        {
          "type": "savedSearches",
          "apiVersion": "2020-08-01",
          "name": "Query using function",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "properties": {
              "etag": "*",
              "displayName": "Unavailable computers",
              "category": "Samples",
              "query": "UnavailableComputers(7days)"
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>参数文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="next-steps"></a>后续步骤

* [获取 Azure Monitor 的其他示例模板](resource-manager-samples.md)。
* [详细了解日志查询](../log-query/log-query-overview.md)。
* [详细了解函数](../log-query/functions.md)。
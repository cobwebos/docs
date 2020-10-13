---
title: Azure Monitor API 停用
description: 介绍旧版本的 Microsoft.operationalinsights 资源提供程序 API 的停用。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: 708901dbc37daa075cf38d3f9ef046ae658ea979
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91744762"
---
# <a name="operationalinsights-api-version-retirement"></a>Microsoft.operationalinsights API 版本停用
Microsoft 将在停用 API 之前至少12个月提供通知，以便顺利过渡到较新的/受支持的版本。 我们发布了新版本 (2020-08-01) **microsoft.operationalinsights** 资源提供程序 api，并将在2023年10月31日停用任何早期的 API 版本。 由于新特性和功能以及优化仅添加到当前 API，因此应尽早升级到最新的 API 版本。

2023年10月31日之后 Azure Monitor 将不再支持比2020-08-01 更早的 Api 版本。 如果你不想升级，则在2023年10月31日之前，将继续由 Azure Monitor 服务提供从早期版本发送的请求。

根据所使用的配置方法，应按照以下示例更新 REST 请求中的新版本，并资源管理器模板：


## <a name="rest"></a>REST
REST API 请求在请求的 URL 中使用 API 版本。 将该版本替换为最新版本，如下面的示例中所示。

```rest
https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
```

## <a name="azure-resource-manager"></a>Azure 资源管理器
资源管理器模板在资源的 **apiVersion** 属性中使用 API 版本。 将该版本替换为最新版本，如下面的示例中所示。


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
              "description": "Name of the workspace."
            }
        },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-08-01",
            "location": "westus",
            "properties": {
                "sku": {
                    "name": "pergb2018"
                },
                "retentionInDays": 30,
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
        }
    ]
  }
}
```


## <a name="next-steps"></a>后续步骤

- 请参阅 [MICROSOFT.OPERATIONALINSIGHTS API 参考](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/allversions)。

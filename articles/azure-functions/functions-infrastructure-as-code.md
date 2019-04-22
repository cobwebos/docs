---
title: 为 Azure Functions 中的函数应用自动执行资源部署 | Microsoft Docs
description: 了解如何生成用于部署函数应用的 Azure 资源管理器模板。
services: Functions
documtationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, functions 无服务体系结构, 基础结构即代码, azure resource manager
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.service: azure-functions
ms.server: functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: glenga
ms.openlocfilehash: 5d028768c062ef7df74d48f83ccc4e27a506f1ac
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270891"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>为 Azure Functions 中的函数应用自动执行资源部署

可以使用 Azure 资源管理器模板来部署函数应用。 本文概述了完成此操作所需的资源和参数。 可能还需要部署其他资源，具体取决于函数应用中的[触发器和绑定](functions-triggers-bindings.md)。

有关创建模板的详细信息，请参阅[创作 Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)。

有关示例模板，请参阅：
- [基于消耗计划的函数应用]
- [基于 Azure 应用服务计划的函数应用]

> [!NOTE]
> Azure Functions 托管的高级计划目前处于预览状态。 有关详细信息，请参阅[Azure Functions 的高级版计划](functions-premium-plan.md)。

## <a name="required-resources"></a>所需资源

Azure Functions 部署这些资源通常包括：

| 资源                                                                           | 要求 | 语法和属性参考                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| 函数应用                                                                     | 需要    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| [Azure 存储](../storage/index.yml)帐户                                   | 需要    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| [Application Insights](../azure-monitor/app/app-insights-overview.md)组件 | 可选    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |   |
| 一个[托管计划](./functions-scale.md)                                             | Optional<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup>托管计划是仅在你选择在上运行函数应用时所需[高级计划](./functions-premium-plan.md)（处于预览状态） 或在[应用服务计划](../app-service/overview-hosting-plans.md)。

> [!TIP]
> 尽管不要求这样做，强烈建议为您的应用程序配置 Application Insights。

<a name="storage"></a>
### <a name="storage-account"></a>存储帐户

函数应用需要 Azure 存储帐户。 你需要一个支持 blob、表、队列和文件的常规用途帐户。 有关详细信息，请参阅 [Azure Functions 存储帐户要求](functions-create-function-app-portal.md#storage-account-requirements)。

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "properties": {
        "accountType": "[parameters('storageAccountType')]"
    }
}
```

此外，在站点配置中，必须将属性 `AzureWebJobsStorage` 指定为应用设置。 如果函数应用未使用 Application Insights 进行监视，还应将 `AzureWebJobsDashboard` 指定为应用设置。

Azure Functions 运行时使用 `AzureWebJobsStorage` 连接字符串创建内部队列。  未启用 Application Insights 时，运行时使用 `AzureWebJobsDashboard` 连接字符串登录到 Azure 表存储并启动门户中的“监视”选项卡。

这些属性在 `siteConfig` 对象中的 `appSettings` 集合中指定：

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

对于监视函数应用，建议使用 application Insights。 与类型定义的 Application Insights 资源**microsoft.insights/components**以及种类**web**:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('functionAppName')]"
            }
        },
```

此外，需要提供给函数应用使用的检测密钥`APPINSIGHTS_INSTRUMENTATIONKEY`应用程序设置。 此属性中指定`appSettings`集合中的`siteConfig`对象：

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>托管计划

托管计划的定义各不相同，并且可以将以下项之一：
* [消耗计划](#consumption)（默认值）
* [高级计划](#premium)（处于预览状态）
* [应用服务计划](#app-service-plan)

### <a name="function-app"></a>函数应用

使用类型的资源定义的函数应用资源**microsoft.web/sites**还使用了**functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
```

> [!IMPORTANT]
> 如果您可以显式定义托管计划，将 dependsOn 数组中所需的其他项： `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

函数应用必须包含这些应用程序设置：

| 设置名称                 | 描述                                                                               | 示例值                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | 存储的连接字符串帐户的内部队列的 Functions 运行时 | 请参阅[存储帐户](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Azure Functions 运行时版本                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | 要用于此应用程序中的函数的语言堆栈                                   | `dotnet``node`， `java`，或 `python` |
| WEBSITE_NODE_DEFAULT_VERSION | 当使用才需要`node`语言堆栈上，指定要使用的版本              | `10.14.1`                             |

这些属性中指定`appSettings`集合中的`siteConfig`属性：

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>基于消耗计划部署

代码运行时，消耗计划会自动分配计算能力，根据处理负载的需要进行扩展，然后在代码停止运行时进行缩减。 无需为空闲 Vm 付费，无需提前保留容量。 若要了解详细信息，请参阅[Azure Functions 的缩放和托管](functions-scale.md#consumption-plan)。

有关 Azure 资源管理器模板示例，请参阅[基于消耗计划的函数应用]。

### <a name="create-a-consumption-plan"></a>创建消耗计划

消耗计划不需要定义。 一个不会自动创建或选择基于每个区域时创建函数应用资源本身。

消耗计划是一种特殊的"serverfarm"资源。 对于 Windows，你可以指定它通过使用`Dynamic`值`computeMode`和`sku`属性：

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

> [!NOTE]
> 消耗计划不能显式定义适用于 Linux。 它将自动创建。

如果显式定义消耗计划，你将需要设置`serverFarmId`上应用，使其指向计划的资源 ID 的属性。 您应确保函数应用具有`dependsOn`计划以及设置。

### <a name="create-a-function-app"></a>创建函数应用

#### <a name="windows"></a>Windows

在 Windows，消耗计划还需要在站点配置中的两个附加设置：`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`和`WEBSITE_CONTENTSHARE`。 这些属性用于配置存储函数应用代码和配置的存储帐户和文件路径。

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

函数应用必须在 Linux 上，其`kind`设置为`functionapp,linux`，并且它必须`reserved`属性设置为`true`:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```



<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>在高级版计划部署

高级计划提供了与消耗计划相同的缩放，但包括专用的资源和其他功能。 若要了解详细信息，请参阅[Azure Functions 高级计划 （预览版）](./functions-premium-plan.md)。

### <a name="create-a-premium-plan"></a>创建高级版计划

高级计划是一种特殊的"serverfarm"资源。 可以通过使用指定它`EP1`， `EP2`，或`EP3`为`sku`属性值。

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>创建函数应用

Function app 上高级版计划必须具有`serverFarmId`属性设置为前面创建的计划的资源 ID。 此外，高级版计划需要在站点配置中的两个附加设置：`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`和`WEBSITE_CONTENTSHARE`。 这些属性用于配置存储函数应用代码和配置的存储帐户和文件路径。

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```


<a name="app-service-plan"></a> 

## <a name="deploy-on-app-service-plan"></a>部署应用服务计划

在应用服务计划中，函数应用在基本、标准和高级 SKU 中的专用 VM 上运行，类似于 Web 应用。 如需详细了解如何使用应用服务计划，请参阅 [Azure 应用服务计划深入概述](../app-service/overview-hosting-plans.md)。

有关 Azure 资源管理器模板示例，请参阅[基于 Azure 应用服务计划的函数应用]。

### <a name="create-an-app-service-plan"></a>创建应用服务计划

应用服务计划被定义的"serverfarm"资源。

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

若要在 Linux 上运行你的应用，还必须设置`kind`到`Linux`:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="create-a-function-app"></a>创建函数应用 

函数应用在应用服务计划必须具有`serverFarmId`属性设置为前面创建的计划的资源 ID。

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

Linux 应用程序还应包括`linuxFxVersion`属性下的`siteConfig`。 如果您只需部署代码，此值取决于您所需的运行时的堆栈：

| 堆栈            | 示例值                                         |
|------------------|-------------------------------------------------------|
| Python（预览版） | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| JavaScript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

你是否[部署自定义容器映像](./functions-create-function-linux-custom-image.md)，则必须指定与该`linuxFxVersion`，包括配置，从而使你的映像以请求，如下所示[用于容器的 Web 应用](/azure/app-service/containers)。 此外，设置`WEBSITES_ENABLE_APP_SERVICE_STORAGE`到`false`，因为你的应用程序内容提供容器本身中：

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>自定义部署

函数应用有许多可用于部署的子资源，包括应用设置和源代码管理选项。 还可以选择删除 **sourcecontrols** 子资源，改用另一个[部署选项](functions-continuous-deployment.md)。

> [!IMPORTANT]
> 若要使用 Azure 资源管理器成功部署应用程序，了解如何在 Azure 中部署资源尤为重要。 在下面的示例中，通过使用 **siteConfig** 应用顶级配置。 请务必在顶级设置这些配置，因为这些配置会将信息传达给 Functions 运行时和部署引擎。 应用 **sourcecontrols/web** 子资源前，需要顶级信息。 虽然可以在子级别 **config/appSettings** 资源中配置这些设置，但在某些情况下，需要在应用 **config/appSettings** *之前*部署函数应用。 比如在[逻辑应用](../logic-apps/index.yml)中使用函数时，函数是另一资源的依赖项。

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "FUNCTIONS_EXTENSION_VERSION": "~2",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> 此模板使用 [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) 应用设置值，这将设置基本目录，Functions 部署引擎 (Kudu) 在此目录中查找可部署代码。 在存储库内，函数位于 **src** 文件夹的子文件夹中。 因此，在前一个示例中，将应用设置值设置为 `src`。 如果函数位于存储库的根目录中，或者不从源代码管理进行部署，则可删除此应用设置值。

## <a name="deploy-your-template"></a>部署模板

可以使用以下任意方法部署模板：

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure 门户](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>“部署到 Azure”按钮

将 ```<url-encoded-path-to-azuredeploy-json>``` 替换为 GitHub 中 `azuredeploy.json` 文件的原始路径的 [URL 编码](https://www.bing.com/search?q=url+encode)版本。

以下示例使用 Markdown：

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

以下示例使用 HTML：

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>后续步骤

深入了解如何开发和配置 Azure Functions。

* [Azure Functions 开发人员参考](functions-reference.md)
* [如何配置 Azure 函数应用设置](functions-how-to-use-azure-function-app-settings.md)
* [创建第一个 Azure 函数](functions-create-first-azure-function.md)

<!-- LINKS -->

[基于消耗计划的函数应用]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[基于 Azure 应用服务计划的函数应用]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json

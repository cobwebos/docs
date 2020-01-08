---
title: 自动部署到 Azure 的函数应用资源
description: 了解如何生成用于部署函数应用的 Azure 资源管理器模板。
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 10efe5d09771f4c5f3a2564ef99ff9cae8cf06c0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433146"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>为 Azure Functions 中的函数应用自动执行资源部署

可以使用 Azure 资源管理器模板来部署函数应用。 本文概述了完成此操作所需的资源和参数。 可能还需要部署其他资源，具体取决于函数应用中的[触发器和绑定](functions-triggers-bindings.md)。

有关创建模板的详细信息，请参阅[创作 Azure 资源管理器模板](../azure-resource-manager/templates/template-syntax.md)。

有关示例模板，请参阅：
- [基于消耗计划的函数应用]
- [基于 Azure 应用服务计划的函数应用]

## <a name="required-resources"></a>所需资源

Azure Functions 部署通常包含以下资源：

| 资源                                                                           | 要求 | 语法和属性参考                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| 函数应用                                                                     | 需要    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| [Azure 存储](../storage/index.yml)帐户                                   | 需要    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| [Application Insights](../azure-monitor/app/app-insights-overview.md)组件 | 可选    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |   |
| [托管计划](./functions-scale.md)                                             | 可选<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup>仅当您选择在[高级计划](./functions-premium-plan.md)（预览中）或[应用服务计划](../app-service/overview-hosting-plans.md)上运行函数应用时，才需要托管计划。

> [!TIP]
> 虽然不是必需的，但强烈建议您为应用程序配置 Application Insights。

<a name="storage"></a>
### <a name="storage-account"></a>存储帐户

函数应用需要 Azure 存储帐户。 你需要一个支持 blob、表、队列和文件的常规用途帐户。 有关详细信息，请参阅 [Azure Functions 存储帐户要求](functions-create-function-app-portal.md#storage-account-requirements)。

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-04-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
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

建议使用 Application Insights 来监视函数应用。 该 Application Insights 资源的定义类型为 " **Microsoft Insights/组件**" 和 " **web**：

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

此外，还需要使用 `APPINSIGHTS_INSTRUMENTATIONKEY` 应用程序设置向函数应用提供检测密钥。 此属性在 `siteConfig` 对象的 `appSettings` 集合中指定：

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>托管计划

托管计划的定义不同，可以是以下各项之一：
* [消耗计划](#consumption)（默认值）
* [高级计划](#premium)（预览版）
* [应用服务计划](#app-service-plan)

### <a name="function-app"></a>函数应用

函数应用资源通过使用类型为 " **functionapp**" 的资源和 **"类型"** 来定义：

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
> 如果要显式定义宿主计划，dependsOn 数组中需要额外的项： `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

函数应用必须包含以下应用程序设置：

| 设置名称                 | Description                                                                               | 示例值                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | 用于内部队列的函数运行时的存储帐户的连接字符串 | 请参阅[存储帐户](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Azure Functions 运行时的版本                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | 要用于此应用中的函数的语言堆栈                                   | `dotnet`、`node`、`java` 或 `python` |
| WEBSITE_NODE_DEFAULT_VERSION | 仅在使用 `node` 语言堆栈时需要，指定要使用的版本              | `10.14.1`                             |

这些属性是在 `siteConfig` 属性的 `appSettings` 集合中指定的：

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

## <a name="deploy-on-consumption-plan"></a>按消耗计划部署

代码运行时，消耗计划会自动分配计算能力，根据处理负载的需要进行扩展，然后在代码停止运行时进行缩减。 无需为空闲 Vm 付费，也不必预先预留容量。 若要了解更多信息，请参阅 [Azure Functions 的缩放和托管](functions-scale.md#consumption-plan)。

有关 Azure 资源管理器模板示例，请参阅[基于消耗计划的函数应用]。

### <a name="create-a-consumption-plan"></a>创建消耗计划

不需要定义消耗计划。 创建函数应用资源本身时，会自动在每个区域中创建或选择一个。

消耗计划是一种特殊类型的 "服务器场" 资源。 对于 Windows，你可以通过使用 `computeMode` 和 `sku` 属性的 `Dynamic` 值来指定它：

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> 不能为 Linux 显式定义消耗计划。 将自动创建它。

如果明确定义了消耗计划，则需要在应用上设置 `serverFarmId` 属性，使其指向计划的资源 ID。 还应确保该函数应用也具有计划的 `dependsOn` 设置。

### <a name="create-a-function-app"></a>创建函数应用

#### <a name="windows"></a>Windows

在 Windows 上，消耗计划需要站点配置中的两个附加设置： `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 和 `WEBSITE_CONTENTSHARE`。 这些属性用于配置存储函数应用代码和配置的存储帐户和文件路径。

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

在 Linux 上，函数应用必须将其 `kind` 设置为 `functionapp,linux`，并且它必须将 `reserved` 属性设置为 `true`：

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

## <a name="deploy-on-premium-plan"></a>部署高级计划

高级计划提供与消耗计划相同的缩放，但包括专用资源和附加功能。 若要了解详细信息，请参阅[Azure Functions 高级计划](./functions-premium-plan.md)。

### <a name="create-a-premium-plan"></a>创建高级计划

高级计划是一种特殊类型的 "服务器场" 资源。 您可以使用 `EP1`、`EP2`或 `EP3` 指定 `sku` 属性值来指定它。

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

高级计划中的函数应用必须将 `serverFarmId` 属性设置为前面创建的计划的资源 ID。 此外，高级计划还需要站点配置中的两个附加设置： "`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`" 和 "`WEBSITE_CONTENTSHARE`"。 这些属性用于配置存储函数应用代码和配置的存储帐户和文件路径。

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

## <a name="deploy-on-app-service-plan"></a>在应用服务计划中部署

在应用服务计划中，函数应用在基本、标准和高级 SKU 中的专用 VM 上运行，类似于 Web 应用。 如需详细了解如何使用应用服务计划，请参阅 [Azure 应用服务计划深入概述](../app-service/overview-hosting-plans.md)。

有关 Azure 资源管理器模板示例，请参阅[基于 Azure 应用服务计划的函数应用]。

### <a name="create-an-app-service-plan"></a>创建应用服务计划

应用服务计划由 "服务器场" 资源定义。

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

若要在 Linux 上运行你的应用，还必须将 `kind` 设置为 `Linux`：

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>创建函数应用 

应用服务计划上的函数应用必须将 `serverFarmId` 属性设置为前面创建的计划的资源 ID。

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

Linux 应用还应在 `siteConfig`下包含 `linuxFxVersion` 属性。 如果只是部署代码，则此值的值由所需的运行时堆栈确定：

| Stack            | 示例值                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
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

如果要[部署自定义容器映像](./functions-create-function-linux-custom-image.md)，则必须使用 `linuxFxVersion` 指定该映像，并包括允许提取映像的配置，如[用于容器的 Web 应用](/azure/app-service/containers)中所示。 此外，将 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 设置为 `false`，因为容器本身中提供了应用内容：

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
> 若要使用 Azure 资源管理器成功部署应用程序，了解如何在 Azure 中部署资源尤为重要。 在下面的示例中，通过使用 **siteConfig** 应用顶级配置。 请务必在顶级设置这些配置，因为这些配置会将信息传达给 Functions 运行时和部署引擎。 应用 **sourcecontrols/web** 子资源前，需要顶级信息。 虽然可以在子级别**config/appSettings**资源中配置这些设置，但在某些情况下，必须在应用**config/appSettings** *之前*部署 function app。 比如在[逻辑应用](../logic-apps/index.yml)中使用函数时，函数是另一资源的依赖项。

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

### <a name="deploy-using-powershell"></a>使用 PowerShell 部署

以下 PowerShell 命令创建一个资源组，并部署一个模板，该模板创建一个具有其所需资源的函数应用。 若要在本地运行，则必须安装[Azure PowerShell](/powershell/azure/install-az-ps) 。 运行[`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount)以登录。

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

若要测试此部署，可以使用[此类模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json)，该模板在消耗计划中的 Windows 上创建函数应用。 将 `<function-app-name>` 替换为 function app 的唯一名称。

## <a name="next-steps"></a>后续步骤

深入了解如何开发和配置 Azure Functions。

* [Azure Functions 开发人员参考](functions-reference.md)
* [如何配置 Azure 函数应用设置](functions-how-to-use-azure-function-app-settings.md)
* [创建第一个 Azure 函数](functions-create-first-azure-function.md)

<!-- LINKS -->

[基于消耗计划的函数应用]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[基于 Azure 应用服务计划的函数应用]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json

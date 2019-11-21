---
title: Automate resource deployment for a function app in Azure Functions
description: 了解如何生成用于部署函数应用的 Azure 资源管理器模板。
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 9c222937831c0e8017a390b16ef192783e9e564a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230526"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>为 Azure Functions 中的函数应用自动执行资源部署

可以使用 Azure 资源管理器模板来部署函数应用。 本文概述了完成此操作所需的资源和参数。 可能还需要部署其他资源，具体取决于函数应用中的[触发器和绑定](functions-triggers-bindings.md)。

有关创建模板的详细信息，请参阅[创作 Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)。

有关示例模板，请参阅：
- [基于消耗计划的函数应用]
- [基于 Azure 应用服务计划的函数应用]

## <a name="required-resources"></a>所需资源

An Azure Functions deployment typically consists of these resources:

| 资源                                                                           | 要求 | Syntax and properties reference                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| 函数应用                                                                     | 需要    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| [Azure 存储](../storage/index.yml)帐户                                   | 需要    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| An [Application Insights](../azure-monitor/app/app-insights-overview.md) component | 可选    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |   |
| A [hosting plan](./functions-scale.md)                                             | Optional<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup>A hosting plan is only required when you choose to run your function app on a [Premium plan](./functions-premium-plan.md) (in preview) or on an [App Service plan](../app-service/overview-hosting-plans.md).

> [!TIP]
> While not required, it is strongly recommended that you configure Application Insights for your app.

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

Application Insights is recommended for monitoring your function apps. The Application Insights resource is defined with the type **Microsoft.Insights/components** and the kind **web**:

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

In addition, the instrumentation key needs to be provided to the function app using the `APPINSIGHTS_INSTRUMENTATIONKEY` application setting. This property is specified in the `appSettings` collection in the `siteConfig` object:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>托管计划

The definition of the hosting plan varies, and can be one of the following:
* [Consumption plan](#consumption) (default)
* [Premium plan](#premium) (in preview)
* [应用服务计划](#app-service-plan)

### <a name="function-app"></a>函数应用

The function app resource is defined by using a resource of type **Microsoft.Web/sites** and kind **functionapp**:

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
> If you are explicitly defining a hosting plan, an additional item would be needed in the dependsOn array: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

A function app must include these application settings：

| 设置名称                 | 描述                                                                               | 示例值                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | A connection string to a storage account that the Functions runtime for internal queueing | See [Storage account](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | The version of the Azure Functions runtime                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | The language stack to be used for functions in this app                                   | `dotnet`, `node`, `java`, or `python` |
| WEBSITE_NODE_DEFAULT_VERSION | Only needed if using the `node` language stack, specifies the version to use              | `10.14.1`                             |

These properties are specified in the `appSettings` collection in the `siteConfig` property:

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

## <a name="deploy-on-consumption-plan"></a>Deploy on Consumption plan

代码运行时，消耗计划会自动分配计算能力，根据处理负载的需要进行扩展，然后在代码停止运行时进行缩减。 You don't have to pay for idle VMs, and you don't have to reserve capacity in advance. 若要了解更多信息，请参阅 [Azure Functions 的缩放和托管](functions-scale.md#consumption-plan)。

有关 Azure 资源管理器模板示例，请参阅[基于消耗计划的函数应用]。

### <a name="create-a-consumption-plan"></a>创建消耗计划

A Consumption plan does not need to be defined. One will automatically be created or selected on a per-region basis when you create the function app resource itself.

The Consumption plan is a special type of "serverfarm" resource. For Windows, you can specify it by using the `Dynamic` value for the `computeMode` and `sku` properties:

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
> The Consumption plan cannot be explicitly defined for Linux. It will be created automatically.

If you do explicitly define your consumption plan, you will need to set the `serverFarmId` property on the app so that it points to the resource ID of the plan. You should ensure that the function app has a `dependsOn` setting for the plan as well.

### <a name="create-a-function-app"></a>创建函数应用

#### <a name="windows"></a>Windows

On Windows, a Consumption plan requires two additional settings in the site configuration: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` and `WEBSITE_CONTENTSHARE`. 这些属性用于配置存储函数应用代码和配置的存储帐户和文件路径。

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

On Linux, the function app must have its `kind` set to `functionapp,linux`, and it must have the `reserved` property set to `true`:

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

## <a name="deploy-on-premium-plan"></a>Deploy on Premium plan

The Premium plan offers the same scaling as the consumption plan but includes dedicated resources and additional capabilities. To learn more, see [Azure Functions Premium Plan](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>创建高级计划

A Premium plan is a special type of "serverfarm" resource. You can specify it by using either `EP1`, `EP2`, or `EP3` for the `sku` property value.

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

A function app on a Premium plan must have the `serverFarmId` property set to the resource ID of the plan created earlier. In addition, a Premium plan requires two additional settings in the site configuration: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` and `WEBSITE_CONTENTSHARE`. 这些属性用于配置存储函数应用代码和配置的存储帐户和文件路径。

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

## <a name="deploy-on-app-service-plan"></a>Deploy on App Service plan

在应用服务计划中，函数应用在基本、标准和高级 SKU 中的专用 VM 上运行，类似于 Web 应用。 如需详细了解如何使用应用服务计划，请参阅 [Azure 应用服务计划深入概述](../app-service/overview-hosting-plans.md)。

有关 Azure 资源管理器模板示例，请参阅[基于 Azure 应用服务计划的函数应用]。

### <a name="create-an-app-service-plan"></a>创建应用服务计划

An App Service plan is defined by a "serverfarm" resource.

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

To run your app on Linux, you must also set the `kind` to `Linux`:

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

A function app on an App Service plan must have the `serverFarmId` property set to the resource ID of the plan created earlier.

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

Linux apps should also include a `linuxFxVersion` property under `siteConfig`. If you are just deploying code, the value for this is determined by your desired runtime stack:

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

If you are [deploying a custom container image](./functions-create-function-linux-custom-image.md), you must specify it with `linuxFxVersion` and include configuration that allows your image to be pulled, as in [Web App for Containers](/azure/app-service/containers). Also, set `WEBSITES_ENABLE_APP_SERVICE_STORAGE` to `false`, since your app content is provided in the container itself:

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

## <a name="customizing-a-deployment"></a>Customizing a deployment

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

### <a name="deploy-using-powershell"></a>使用 PowerShell 部署

The following PowerShell commands create a resource group and deploy a template that create a function app with its required resources. To run locally, you must have [Azure PowerShell](/powershell/azure/install-az-ps) installed. Run [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) to sign in.

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

To test out this deployment, you can use a [template like this one](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) that creates a function app on Windows in a Consumption plan. Replace `<function-app-name>` with a unique name for your function app.

## <a name="next-steps"></a>后续步骤

深入了解如何开发和配置 Azure Functions。

* [Azure Functions 开发人员参考](functions-reference.md)
* [如何配置 Azure 函数应用设置](functions-how-to-use-azure-function-app-settings.md)
* [创建第一个 Azure 函数](functions-create-first-azure-function.md)

<!-- LINKS -->

[基于消耗计划的函数应用]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[基于 Azure 应用服务计划的函数应用]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json

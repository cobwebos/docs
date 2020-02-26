---
title: 使用 REST 管理 ML 资源
titleSuffix: Azure Machine Learning
description: 如何使用 REST Api 创建、运行和删除 Azure ML 资源
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: 419dbd998abc5cbd2da64a990e13d46f3fb2efbe
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77580621"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>使用 REST 创建、运行和删除 Azure ML 资源

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

可以通过多种方式来管理 Azure ML 资源。 可以使用[门户](https://portal.azure.com/)、[命令行接口](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)或[Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。 或者，您可以选择 REST API。 REST API 以标准方式使用 HTTP 谓词来创建、检索、更新和删除资源。 REST API 适用于可发出 HTTP 请求的任何语言或工具。 REST 的简单结构通常使其成为脚本环境和 MLOps 自动化的不错选择。 

在本文中，学习如何：

> [!div class="checklist"]
> * 检索授权令牌
> * 使用服务主体身份验证创建格式正确的 REST 请求
> * 使用 GET 请求检索有关 Azure ML 层次结构资源的信息
> * 使用 PUT 和 POST 请求创建和修改资源
> * 使用 DELETE 请求清理资源 
> * 使用基于密钥的授权来评分已部署的模型

## <a name="prerequisites"></a>必备条件

- 你对其具有管理权限的**Azure 订阅**。 如果你没有订阅，请尝试[免费或付费的个人订阅](https://aka.ms/AMLFree)
- [Azure 机器学习工作区](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- 管理 REST 请求使用服务主体身份验证。 按照为[Azure 机器学习资源和工作流设置身份验证](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication)中的步骤在你的工作区中创建服务主体
- **卷曲**的实用程序。 **卷**程序在适用于 Linux 的[Windows 子系统](https://aka.ms/wslinstall/)或任何 UNIX 分发中可用。 在 PowerShell 中，**卷曲**是**WebRequest**的别名，`curl -d "key=val" -X POST uri` 变成 `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`。 

## <a name="retrieve-a-service-principal-authentication-token"></a>检索服务主体身份验证令牌

管理 REST 请求使用 OAuth2 隐式流进行身份验证。 此身份验证流使用你的订阅的服务主体提供的令牌。 若要检索此令牌，需要：

- 租户 ID （标识订阅所属的组织）
- 你的客户端 ID （将与创建的令牌关联）
- 你的客户端机密（你应该保护的）

应按照为[Azure 机器学习资源和工作流设置身份验证](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication)中所述，从响应创建服务主体中获取这些值。 如果你使用的是公司订阅，你可能没有创建服务主体的权限。 在这种情况下，应使用[免费或付费的个人订阅](https://aka.ms/AMLFree)。

检索令牌：

1. 打开终端窗口
1. 在命令行中输入以下代码
1. 用自己的值替换 `{your-tenant-id}`、`{your-client-id}`和 `{your-client-secret}`。 在本文中，括在大括号中的字符串是需要替换为自己适当的值的变量。
1. 运行命令

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

响应应提供一小时的访问令牌：

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

记下该令牌，因为你将使用该令牌对所有后续的管理请求进行身份验证。 通过在所有请求中设置授权标头来实现此目的：

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

请注意，该值以字符串 "持有者" 开头，其中包括单个空格，然后添加令牌。

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>获取与你的订阅关联的资源组的列表

若要检索与订阅关联的资源组的列表，请运行：

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

在 Azure 中，发布了许多 REST Api。 每个服务提供程序在其自己的节奏上更新其 API，但这样做不会破坏现有的程序。 服务提供程序使用 `api-version` 参数以确保兼容性。 `api-version` 参数因服务而异。 例如，对于机器学习服务，将 `2019-11-01`当前的 API 版本。 对于存储帐户，它 `2019-06-01`。 对于密钥保管库，`2019-09-01`。 所有 REST 调用都应将 `api-version` 参数设置为预期值。 即使 API 继续演化，你也可以依赖于指定版本的语法和语义。 如果向提供程序发送请求时没有 `api-version` 参数，则响应将包含支持的值的可读列表。 

上述调用将导致以下格式的压缩 JSON 响应： 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>向下钻取工作区及其资源

若要检索资源组中的工作区集，请运行以下，并将 `{your-subscription-id}`、`{your-resource-group}`和 `{your-access-token}`： 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

同样，您将收到一个 JSON 列表，这一次包含一个列表，其中的每个项都详细说明了工作区：

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

若要处理工作区中的资源，需从常规**management.azure.com**服务器切换到特定于工作区位置的 REST API 服务器。 请注意上面的 JSON 响应中 `discoveryUrl` 项的值。 如果收到该 URL，会收到类似于下面的响应：

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

`api` 响应的值是要用于其他请求的服务器的 URL。 例如，若要列出试验，请发送以下命令。 将 `regional-api-server` 替换为 `api` 响应的值（例如，`centralus.api.azureml.ms`）。 同时，像往常一样替换 `your-subscription-id`、`your-resource-group`、`your-workspace-name`和 `your-access-token`：

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

同样，若要在工作区中检索已注册的模型，请发送：

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

请注意，若要列出试验，路径从 `history/v1.0` 开始列出模型时，路径以 `modelmanagement/v1.0`开头。 REST API 划分为多个操作组，每个操作组具有不同的路径。 以下链接中的 API 参考文档列出了各种操作的操作、参数和响应代码。

|区域|路径|参考|
|-|-|-|
|项目|项目/v2.0/|[REST API 参考](https://docs.microsoft.com/rest/api/azureml/artifacts)|
|数据存储|数据存储/v1.0/|[REST API 参考](https://docs.microsoft.com/rest/api/azureml/datastores)|
|超参数优化|hyperdrive/v1.0/|[REST API 参考](https://docs.microsoft.com/rest/api/azureml/hyperparametertuning)|
|Models|modelmanagement/v1.0/|[REST API 参考](https://docs.microsoft.com/rest/api/azureml/modelsanddeployments/mlmodels)|
|运行历史记录|执行/v1.0 1.0/和 history/v1.0/|[REST API 参考](https://docs.microsoft.com/rest/api/azureml/runs)|

您可以使用的常规模式浏览 REST API：

|URL 组件|示例|
|-|-|
| https://| |
| 区域 api-服务器/ | centralus.api.azureml.ms/ |
| 操作-路径/ | history/v1.0/ |
| 订阅/{你的订阅-id}/ | 订阅/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/{你的资源组}/ | resourceGroups/MyResourceGroup/ |
| 提供程序/操作提供程序/ | providers/MachineLearningServices/ |
| 提供程序-资源-路径/ | workspace/MLWorkspace/MyWorkspace/FirstExperiment/运行/1/ |
| 操作-终结点/ | 项目/元数据/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>使用 PUT 和 POST 请求创建和修改资源

除了通过 GET 谓词进行资源检索外，REST API 支持创建定型、部署和监视 ML 解决方案所需的所有资源。 

培训和运行 ML 模型需要计算资源。 可以通过以下方式列出工作区的计算资源： 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

若要创建或覆盖命名计算资源，请使用 PUT 请求。 在下面的中，除了对 `your-subscription-id`、`your-resource-group`、`your-workspace-name`和 `your-access-token`的常用替换以外，`your-compute-name`、`location`、`vmSize`、`vmPriority`、`scaleSettings`和 `adminUserName`替换 `adminUserPassword`和值。 按照[机器学习计算-创建或更新 SDK 参考](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate)中的引用中所述，以下命令将创建一个专用的单节点 Standard_D1 （一个基本 CPU 计算资源），该资源将在30分钟后缩减：

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> 在 Windows 终端中，你可能需要在发送 JSON 数据时对双引号符号进行转义。 也就是说，`"location"` 的文本将变为 `\"location\"`。 

成功的请求将获取 `201 Created` 的响应，但请注意，此响应只是指预配过程已开始。 你需要投票（或使用门户）来确认它是否成功完成。

### <a name="create-an-experimental-run"></a>创建实验性运行

若要在实验中开始运行，需要包含定型脚本和相关文件的 zip 文件夹，以及运行定义 JSON 文件。 Zip 文件夹的根目录中必须有 Python 条目文件。 例如，在名为 "**火车 .zip**" 的文件夹中，将普通 Python 计划（如以下）压缩为一个文件夹。

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

将此下一个代码段保存为**定义 json**。 确认 "脚本" 值与刚刚压缩的 Python 文件的名称匹配。 确认 "目标" 值与可用计算资源的名称匹配。 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

使用 `multipart/form-data` 内容将这些文件发布到服务器：

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

成功的 POST 请求将生成 `200 OK` 状态，其中包含所创建的运行的标识符的响应正文：

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

你可以使用现在应熟悉的 rest-ful 模式监视运行：

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>删除不再需要的资源

某些（但不是全部）资源支持删除谓词。 在提交到 REST API 以删除用例之前，请检查[API 引用](https://docs.microsoft.com/rest/api/azureml/)。 例如，若要删除某一模型，可以使用：

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>使用 REST 对已部署的模型进行评分

尽管可以部署一个模型，使其能够使用服务主体进行身份验证，但大多数面向客户端的部署使用基于密钥的身份验证。 可以在 Studio 的 "**终结点**" 选项卡的部署页中找到相应的密钥。 同一位置将显示终结点的计分 URI。 必须将模型的输入建模为名为 `data`的 JSON 数组：

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>使用 REST 创建工作区 

每个 Azure ML 工作区依赖于四个其他 Azure 资源：启用了管理的容器注册表、密钥保管库、Application Insights 资源和存储帐户。 在这些资源存在之前，你无法创建工作区。 有关创建每个此类资源的详细信息，请参阅 REST API 参考。

若要创建工作区，请将类似于下面的调用添加到 `management.azure.com`。 尽管此调用需要设置大量变量，但与本文讨论的其他调用在结构上是相同的。 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

你应收到 `202 Accepted` 响应，并在返回的标头中 `Location` URI。 你可以获取此 URI 以获取有关部署的信息，包括有用的调试信息（如果某个依赖资源存在问题）（例如，如果你忘记在容器注册表中启用管理员访问权限）。 

## <a name="troubleshooting"></a>故障排除

### <a name="resource-provider-errors"></a>资源提供程序错误

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>移动工作区

> [!WARNING]
> 不支持将 Azure 机器学习工作区移动到不同的订阅，或将拥有的订阅移动到新租户。 这样做可能会导致错误。

### <a name="deleting-the-azure-container-registry"></a>删除 Azure 容器注册表

Azure 机器学习工作区对某些操作使用 Azure 容器注册表（ACR）。 它首次需要时，它将自动创建一个 ACR 实例。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>后续步骤

- 了解完整的[AzureML REST API 引用](https://docs.microsoft.com/rest/api/azureml/)。
- 了解如何使用 Studio & 设计器[通过设计器（预览）预测汽车价格](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score)。
- 探索[Jupyter 笔记本 Azure 机器学习](https://docs.microsoft.com/azure//machine-learning/samples-notebooks)。

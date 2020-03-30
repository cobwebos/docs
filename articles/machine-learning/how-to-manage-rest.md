---
title: 使用 REST 管理 ML 资源
titleSuffix: Azure Machine Learning
description: 如何使用 REST API 创建、运行和删除 Azure ML 资源
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: 419dbd998abc5cbd2da64a990e13d46f3fb2efbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77580621"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>使用 REST 创建、运行和删除 Azure ML 资源

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

有几种方法可以管理 Azure ML 资源。 您可以使用[门户](https://portal.azure.com/)、[命令行接口](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)或[Python SDK。](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 或者，您可以选择 REST API。 REST API 以标准方式使用 HTTP 谓词来创建、检索、更新和删除资源。 REST API 适用于任何可以发出 HTTP 请求的语言或工具。 REST 的直观结构通常使其成为脚本环境和 MLOps 自动化的理想选择。 

在本文中，学习如何：

> [!div class="checklist"]
> * 检索授权令牌
> * 使用服务主体身份验证创建格式正确的 REST 请求
> * 使用 GET 请求检索有关 Azure ML 分层资源的信息
> * 使用 PUT 和 POST 请求创建和修改资源
> * 使用 DELETE 请求清理资源 
> * 使用基于密钥的授权对已部署的模型进行评分

## <a name="prerequisites"></a>先决条件

- 具有管理权限的**Azure 订阅**。 如果您没有此类订阅，请尝试[免费或付费个人订阅](https://aka.ms/AMLFree)
- [Azure 机器学习工作区](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- 管理 REST 请求使用服务主体身份验证。 按照[设置 Azure 机器学习资源和工作流的身份验证](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication)中的步骤在工作区中创建服务主体
- **卷曲**实用程序。 **curl**程序在适用于 Linux 或任何 UNIX 分发的[Windows 子系统](https://aka.ms/wslinstall/)中可用。 在 PowerShell 中，**卷曲**是**Invoke-Web** `curl -d "key=val" -X POST uri`请求`Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`的别名，并变为 。 

## <a name="retrieve-a-service-principal-authentication-token"></a>检索服务主体身份验证令牌

管理 REST 请求使用 OAuth2 隐式流进行身份验证。 此身份验证流使用订阅的服务主体提供的令牌。 要检索此令牌，您需要：

- 租户 ID（标识订阅所属的组织）
- 客户端 ID（将与创建的令牌关联）
- 您的客户机密（应保护）

应具有这些值，从响应到创建服务主体，如[设置 Azure 机器学习资源和工作流的身份验证](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication)中所述。 如果您使用的是公司订阅，则可能没有创建服务主体的权限。 在这种情况下，您应该使用[免费或付费的个人订阅](https://aka.ms/AMLFree)。

要检索令牌：

1. 打开终端窗口
1. 在命令行中输入以下代码
1. 将您自己的值替换为`{your-tenant-id}`和`{your-client-id}`。 `{your-client-secret}` 在本文中，由卷曲括号包围的字符串是您必须用您自己的适当值替换的变量。
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

记下令牌，因为您将使用它对所有后续管理请求进行身份验证。 为此，您将在所有请求中设置授权标头：

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

请注意，该值以字符串"Bearer"开头，包括添加令牌之前的单个空格。

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>获取与订阅关联的资源组列表

要检索与订阅关联的资源组列表，请运行：

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

在整个 Azure 中，许多 REST API 都发布。 每个服务提供商都会按照自己的节奏更新其 API，但不会破坏现有程序。 服务提供商使用 参数`api-version`来确保兼容性。 参数`api-version`因服务而异。 例如，对于机器学习服务，当前 API 版本为`2019-11-01`。 对于存储帐户，它是`2019-06-01`。 对于密钥保管库，它是`2019-09-01`。 所有 REST 调用都应`api-version`将参数设置为预期值。 即使 API 不断发展，也可以依赖指定版本的语法和语义。 如果向没有`api-version`参数的提供程序发送请求，响应将包含受支持的值的人工可读列表。 

上述调用将导致表单的压缩 JSON 响应： 

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


## <a name="drill-down-into-workspaces-and-their-resources"></a>深入了解工作区及其资源

若要检索资源组中的工作区集，运行以下内容、替换`{your-subscription-id}`和`{your-resource-group}`： `{your-access-token}` 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

再次，您将收到一个 JSON 列表，这次包含一个列表，其中每个项都详细说明了工作区：

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

要处理工作区中的资源，您需要从常规**management.azure.com**服务器切换到特定于工作区位置的 REST API 服务器。 请注意上述 JSON`discoveryUrl`响应中密钥的值。 如果您获取该 URL，您将收到类似这样的响应：

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

`api`响应的值是用于其他请求的服务器 URL。 例如，要列出实验，请发送以下命令。 替换为`regional-api-server``api`响应的值（例如， `centralus.api.azureml.ms` 还像往常`your-subscription-id``your-access-token`一`your-resource-group`样`your-workspace-name`替换 、和：

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

请注意，要列出路径在`history/v1.0`列出模型时开始的实验，路径从`modelmanagement/v1.0`开始。 REST API 分为多个操作组，每个组具有不同的路径。 以下链接的 API 参考文档列出了各种操作的操作、参数和响应代码。

|区域|路径|参考|
|-|-|-|
|项目|工件/v2.0/|[REST API 参考](https://docs.microsoft.com/rest/api/azureml/artifacts)|
|数据存储|数据存储/v1.0/|[REST API 参考](https://docs.microsoft.com/rest/api/azureml/datastores)|
|超参数调整|超驱动器/v1.0/|[REST API 参考](https://docs.microsoft.com/rest/api/azureml/hyperparametertuning)|
|Models|模型管理/v1.0/|[REST API 参考](https://docs.microsoft.com/rest/api/azureml/modelsanddeployments/mlmodels)|
|运行历史记录|执行/v1.0/ 和历史/v1.0/|[REST API 参考](https://docs.microsoft.com/rest/api/azureml/runs)|

您可以使用以下一般模式浏览 REST API：

|URL 组件|示例|
|-|-|
| https://| |
| 区域-api 服务器/ | centralus.api.azureml.ms/ |
| 操作路径/ | 历史记录/v1.0/ |
| 订阅/[您的订阅 id]/ | 订阅/abcde123-abab-abab-1234-0123456789abc/ |
| 资源组/[您的资源组]/ | 资源组/我的资源组/ |
| 供应商/运营提供商/ | 提供商/微软.机器学习服务/ |
| 提供程序-资源路径/ | 工作区/ML工作区/我的工作区/第一次尝试/运行/1/ |
| 操作-终结点/ | 项目/元数据/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>使用 PUT 和 POST 请求创建和修改资源

除了使用 GET 谓词进行资源检索外，REST API 还支持创建训练、部署和监视 ML 解决方案所需的所有资源。 

训练和运行 ML 模型需要计算资源。 可以使用以下方式列出工作区的计算资源： 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

要创建或覆盖指定的计算资源，将使用 PUT 请求。 在以下项中，除了现在熟悉的替换`your-subscription-id` `your-resource-group`、`your-workspace-name`和`your-access-token`， 和`your-compute-name`， 和`location`的值 中`vmSize` `vmPriority`， `scaleSettings` `adminUserName`、`adminUserPassword`和 。 如[机器学习计算 - 创建或更新 SDK 引用中的引用](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate)中所述，以下命令将创建专用的单节点Standard_D1（基本 CPU 计算资源），该Standard_D1将在 30 分钟后缩小规模：

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
> 在 Windows 终端中，在发送 JSON 数据时，您可能需要转义双引号。 也就是说，文本（如`"location"`变为`\"location\"`）。 

成功的请求将得到响应`201 Created`，但请注意，此响应仅意味着预配过程已经开始。 您需要轮询（或使用门户）以确认其成功完成。

### <a name="create-an-experimental-run"></a>创建实验运行

要在实验中开始运行，您需要一个包含训练脚本和相关文件的 zip 文件夹，以及运行定义 JSON 文件。 zip 文件夹的根目录中必须包含 Python 条目文件。 例如，将一个琐碎的 Python 程序（如下所示）压缩到名为**train.zip 的**文件夹中。

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

将下一个代码段另存为**定义.json**。 确认"脚本"值与您刚刚压缩的 Python 文件的名称匹配。 确认"目标"值与可用计算资源的名称匹配。 

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

使用`multipart/form-data`内容将这些文件发布到服务器：

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

成功的 POST 请求将生成`200 OK`状态，响应正文包含创建的运行的标识符：

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

您可以使用现在应该熟悉的 REST 模式监视运行：

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>删除不再需要的资源

一些（但不是全部）资源支持 DELETE 谓词。 在提交到 REST API 之前，请检查[API 参考](https://docs.microsoft.com/rest/api/azureml/)，以便删除用例。 例如，要删除模型，可以使用：

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>使用 REST 对已部署的模型进行评分

虽然可以部署模型以便使用服务主体进行身份验证，但大多数面向客户端的部署都使用基于密钥的身份验证。 您可以在 Studio 的 **"终结点"** 选项卡中查找部署页面中的相应密钥。 同一位置将显示终结点的评分 URI。 模型的输入必须建模为 JSON 数组，名为`data`：

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>使用 REST 创建工作区 

每个 Azure ML 工作区都依赖于其他四个 Azure 资源：启用管理的容器注册表、密钥保管库、应用程序见解资源和存储帐户。 在这些资源存在之前，无法创建工作区。 有关创建每个此类资源的详细信息，请参阅 REST API 参考。

要创建工作区，请发出类似于 下面的调用`management.azure.com`。 虽然此调用要求您设置大量变量，但它在结构上与本文讨论的其他调用相同。 

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

您应该接收响应`202 Accepted`，并在返回的标头中接收`Location`URI。 您可以获取此 URI 以获取有关部署的信息，包括如果其中一个从属资源出现问题（例如，如果您忘记在容器注册表上启用管理员访问），则提供有用的调试信息。 

## <a name="troubleshooting"></a>疑难解答

### <a name="resource-provider-errors"></a>资源提供程序错误

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>移动工作区

> [!WARNING]
> 不支持将 Azure 机器学习工作区移动到其他订阅，或将所属订阅移动到新租户。 这样做可能会导致错误。

### <a name="deleting-the-azure-container-registry"></a>删除 Azure 容器注册表

Azure 机器学习工作区使用 Azure 容器注册表 （ACR） 执行某些操作。 当 ACR 实例首次需要时，它将自动创建一个 ACR 实例。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>后续步骤

- 浏览完整的[AzureML REST API 引用](https://docs.microsoft.com/rest/api/azureml/)。
- 了解如何使用工作室&设计师[预测汽车价格与设计师（预览）。](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score)
- [使用聚居笔记本](https://docs.microsoft.com/azure//machine-learning/samples-notebooks)探索 Azure 机器学习。

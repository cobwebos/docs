---
title: 创建具有 Azure CLI 的工作区
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure CLI 创建新的 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 11/05/2019
ms.openlocfilehash: f22145541369093f9cb8a2e9d06b705b6b2121df
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289860"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>使用 Azure CLI 创建 Azure 机器学习的工作区
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何使用 Azure CLI 创建 Azure 机器学习工作区。 Azure CLI 提供了用于管理 Azure 资源的命令。 CLI 的机器学习扩展提供了用于处理 Azure 机器学习资源的命令。

## <a name="prerequisites"></a>必备组件

* 一个 **Azure 订阅**。 如果没有，请尝试[Azure 机器学习免费或付费版本](https://aka.ms/AMLFree)。

* 若要在**本地环境**中使用本文档中的 CLI 命令，需要[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

    如果使用[Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)，则可以通过浏览器访问 CLI，并将其驻留在云中。

## <a name="connect-the-cli-to-your-azure-subscription"></a>将 CLI 连接到 Azure 订阅

> [!IMPORTANT]
> 如果使用 Azure Cloud Shell，则可以跳过此部分。 Cloud shell 使用登录到 Azure 订阅的帐户自动对你进行身份验证。

可以通过多种方法从 CLI 对 Azure 订阅进行身份验证。 最基本的使用浏览器进行交互身份验证。 若要以交互方式进行身份验证，请打开命令行或终端并使用以下命令：

```azurecli
az login
```

如果 CLI 可以打开默认的浏览器，则它会打开该浏览器并加载登录页。 否则，你需要打开浏览器并按照命令行中的说明进行操作。 说明涉及浏览到[https://aka.ms/devicelogin](https://aka.ms/devicelogin)并输入授权代码。

有关其他身份验证方法，请参阅[登录 Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

## <a name="install-the-machine-learning-extension"></a>安装机器学习扩展

若要安装机器学习扩展，请使用以下命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>创建工作区

Azure 机器学习工作区依赖于以下 Azure 服务或实体：

> [!IMPORTANT]
> 如果未指定现有的 Azure 服务，则会在创建工作区期间自动创建一个。 必须始终指定资源组。

| 服务 | 用于指定现有实例的参数 |
| ---- | ---- |
| **Azure 资源组** | `-g <resource-group-name>`
| **Azure 存储帐户** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure 容器注册表** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>创建资源组

必须在资源组中创建 Azure 机器学习工作区。 可以使用现有资源组，也可以创建新组。 若要__创建新的资源组__，请使用以下命令。 将 `<resource-group-name>` 替换为此资源组要使用的名称。 将 `<location>` 替换为要用于此资源组的 Azure 区域：

> [!TIP]
> 应选择可用 Azure 机器学习区域。 有关信息，请参阅[可用产品（按区域](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)）。

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

此命令的响应类似于以下 JSON：

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

有关使用资源组的详细信息，请参阅[az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)。

### <a name="automatically-create-required-resources"></a>自动创建所需的资源

若要创建__自动创建服务__的新工作区，请使用以下命令：

> [!TIP]
> 本部分中的命令将创建一个基本版本工作区。 若要创建企业工作区，请将 `--sku enterprise` 开关与 `az ml workspace create` 命令一起使用。 有关 Azure 机器学习版本的详细信息，请参阅[什么是 Azure 机器学习](overview-what-is-azure-ml.md#sku)。

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

此命令的输出类似于以下 JSON：

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="use-existing-resources"></a>使用现有资源

若要创建使用现有资源的工作区，必须提供资源的 ID。 使用以下命令获取服务的 ID：

> [!IMPORTANT]
> 无需指定所有现有资源。 您可以指定一个或多个。 例如，可以指定现有的存储帐户，工作区将创建其他资源。

+ **Azure 存储帐户**： `az storage account show --name <storage-account-name> --query "id"`

    此命令的响应类似于以下文本，是存储帐户的 ID：

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Azure Application Insights**：

    1. 安装 application insights 扩展：

        ```bash
        az extension add -n application-insights
        ```

    2. 获取 application insights 服务的 ID：

        ```bash
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        此命令的响应类似于以下文本，是 application insights 服务的 ID：

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**： `az keyvault show --name <key-vault-name> --query "ID"`

    此命令的响应类似于以下文本，是 key vault 的 ID：

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure 容器注册表**： `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    此命令的响应类似于以下文本，是容器注册表的 ID：

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > 容器注册表必须先启用[管理员帐户](/azure/container-registry/container-registry-authentication#admin-account)，然后才能将其与 Azure 机器学习工作区一起使用。

如果你有要用于工作区的资源的 Id，请使用 base `az workspace create -w <workspace-name> -g <resource-group-name>` 命令，然后添加现有资源的参数和 ID （s）。 例如，以下命令将创建一个使用现有容器注册表的工作区：

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

此命令的输出类似于以下 JSON：

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>列出工作区

若要列出 Azure 订阅的所有工作区，请使用以下命令：

```azurecli-interactive
az ml workspace list
```

此命令的输出类似于以下 JSON：

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

有关详细信息，请参阅[az ml 工作区列表](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list)文档。

## <a name="get-workspace-information"></a>获取工作区信息

若要获取有关工作区的信息，请使用以下命令：

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

此命令的输出类似于以下 JSON：

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

有关详细信息，请参阅[az ml 工作区显示](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show)文档。

## <a name="update-a-workspace"></a>更新工作区

若要更新工作区，请使用以下命令：

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

此命令的输出类似于以下 JSON：

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

有关详细信息，请参阅[az ml 工作区更新](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update)文档。

## <a name="share-a-workspace-with-another-user"></a>与其他用户共享工作区

若要与订阅中的其他用户共享工作区，请使用以下命令：

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

有关 Azure 机器学习的基于角色的访问控制（RBAC）的详细信息，请参阅[管理用户和角色](how-to-assign-roles.md)。

有关详细信息，请参阅[az ml 工作区共享](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share)文档。

## <a name="sync-keys-for-dependent-resources"></a>依赖资源的同步密钥

如果更改了工作区使用的某个资源的访问密钥，请使用以下命令将新密钥与工作区同步：

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

有关更改密钥的详细信息，请参阅[重新生成存储访问密钥](how-to-change-storage-access-key.md)。

有关详细信息，请参阅[az ml 工作区同步密钥](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys)文档。

## <a name="delete-a-workspace"></a>创建工作区

若要在不再需要某个工作区后将其删除，请使用以下命令：

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> 删除工作区不会删除工作区使用的 application insights、存储帐户、密钥保管库或容器注册表。

还可以删除资源组，该资源组将删除资源组中的工作区和所有其他 Azure 资源。 若要删除资源组，请使用以下命令：

```azurecli-interactive
az group delete -g <resource-group-name>
```

有关详细信息，请参阅[az ml 工作区删除](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete)文档。

## <a name="troubleshooting"></a>故障排除

### <a name="resource-provider-errors"></a>资源提供程序错误

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

## <a name="next-steps"></a>后续步骤

有关机器学习的 Azure CLI 扩展的详细信息，请参阅[az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)文档。

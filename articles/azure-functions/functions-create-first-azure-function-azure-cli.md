---
title: "通过 Azure CLI 创建第一个函数 | Microsoft Docs"
description: "了解如何使用 Azure CLI 创建第一个可无服务器执行的 Azure Function。"
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 05/02/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: erikre
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 2292b35819c5a98b690041e10f6e6d1a93fa7837
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017

---

# <a name="create-your-first-function-using-the-azure-cli"></a>使用 Azure CLI 创建第一个函数

本快速入门教程逐步讲解如何使用 Azure Functions 建第一个函数。 使用 Azure CLI 创建 Function App（托管函数的无服务器基础结构）。 函数代码本身将从 GitHub 示例存储库部署。    

可以使用 Mac、Windows 或 Linux 计算机执行以下步骤。 

## <a name="prerequisites"></a>先决条件 

运行此示例之前，必须做好以下准备：

+ 一个有效的 [GitHub](https://github.com) 帐户。 
+ 一个有效的 Azure 订阅。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 


## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源（例如函数应用、数据库和存储帐户）的逻辑容器。

以下示例创建名为 `myResourceGroup` 的资源组：

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

Functions 使用 Azure 存储帐户来保留状态以及有关函数的其他信息。 使用 [az storage account create](/cli/azure/storage/account#create) 命令在创建的资源组中创建存储帐户。

在以下命令中，请将 `<storage_name>` 占位符替换为你自己的全局唯一存储帐户名。 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

创建存储帐户后，Azure CLI 将显示类似于以下示例的信息：

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```

## <a name="create-a-function-app"></a>创建 Function App

必须使用 Function App 托管函数的执行。 Function App 提供一个环境，以便在不使用服务器的情况下执行函数代码。 它可让你将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。 使用 [az functionapp create](/cli/azure/functionapp#create) 命令创建 Function App。 

在以下命令中，请将 `<app_name>` 占位符替换为你自己的唯一 Function App 名称，将 `<storage_name>` 替换为存储帐户名。 `<app_name>` 将用作 Function App 的默认 DNS 域，因此，该名称需要在 Azure 中的所有应用之间保持唯一。 

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup --consumption-plan-location westeurope
```
默认情况下，使用的 Function App 附带消耗托管计划，这意味着，系统会根据函数的需要动态添加资源，你只需在运行函数时付费。 有关详细信息，请参阅[选择适当的托管计划](functions-scale.md)。 

创建 Function App 后，Azure CLI 将显示类似于以下示例的信息：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

创建 Function App 后，可以从 GitHub 示例存储库部署实际函数代码。

## <a name="deploy-your-function-code"></a>部署函数代码  

可使用多种方法在新的 Function App 中创建函数代码。 本主题将连接到 GitHub 中的示例存储库。 与前面一样，请将以下代码中的 `<app_name>` 占位符替换为创建的 Function App 的名称。 

```azurecli-interactive
az functionapp deployment source config --name <app_name> --resource-group myResourceGroup --repo-url https://github.com/Azure-Samples/functions-quickstart --branch master --manual-integration
```
设置部署源后，Azure CLI 将显示类似于以下示例的信息（为方便阅读，已删除 null 值）：

```json
{
  "branch": "master",
  "deploymentRollbackEnabled": false,
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myResourceGroup/...",
  "isManualIntegration": true,
  "isMercurial": false,
  "location": "West Europe",
  "name": "quickstart",
  "repoUrl": "https://github.com/Azure-Samples/functions-quickstart",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.Web/sites/sourcecontrols"
}
```

## <a name="test-the-function"></a>测试函数

在 Mac 或 Linux 计算机上使用 cURL 或者在 Windows 上使用 Bash 测试已部署的函数。 执行以下 cURL 命令（请将 `<app_name>` 占位符替换为 Function App 的名称）。 在 URL 的后面附加查询字符串 `&name=<yourname>`。

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![函数响应将显示在浏览器中。](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-curl.png)  

如果无法在命令行中使用 cURL，只需在 Web 浏览器的地址栏中输入相同的 URL。 同样，请将 `<app_name>` 占位符替换为 Function App 的名称，在 URL 的后面附加查询字符串 `&name=<yourname>`，然后执行请求。 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![函数响应将显示在浏览器中。](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-browser.png)  

## <a name="clean-up-resources"></a>清理资源

本教程系列中的其他快速入门教程是在本文的基础上制作的。 如果你打算继续学习后续的快速入门或相关教程，请不要清除在本快速入门中创建的资源。 如果你不打算继续学习，请使用以下命令删除通过本快速入门创建的所有资源：

```azurecli-interactive
az group delete --name myResourceGroup
```
出现提示时请键入 `y`。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]


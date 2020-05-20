---
title: 教程 - 将应用部署到 Azure Service Fabric 网格
description: 本教程介绍如何使用模板将应用程序部署到 Service Fabric 网格。
author: dkkapur
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 1ff1407400843fdb0f0ff997e2e0a3c1b7e67c7d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "75494937"
---
# <a name="tutorial-deploy-an-application-to-service-fabric-mesh-using-a-template"></a>教程：使用模板将应用程序部署到 Service Fabric 网格

本教程是一个系列中的第一部分。 将了解如何使用模板部署 Azure Service Fabric 网格应用程序。  该应用程序由 ASP.NET Web 前端服务和 ASP.NET Core Web API 后端服务组成，这些服务可在 Docker 中心找到。  从 Docker 中心拉取两个容器映像，然后将其推送到你自己的专用注册表。 然后，为应用程序创建 Azure RM 模板并将应用程序从容器注册表部署到 Service Fabric 网格。 完成后，将在 Service Fabric 网格中运行一个简单的待办事项应用程序。

在该系列的第一部分中，你会学习如何：

> [!div class="checklist"]
> * 创建专用 Azure 容器注册表实例
> * 将容器映像推送到该注册表
> * 创建 RM 模板和参数文件
> * 将应用程序部署到 Service Fabric 网格

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * 使用模板将应用程序部署到 Service Fabric 网格
> * [在 Service Fabric 网格中运行的应用程序内扩展服务](service-fabric-mesh-tutorial-template-scale-services.md)
> * [升级 Service Fabric 网格中运行的应用程序](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [删除应用程序](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>必备条件

在开始学习本教程之前：

* 如果没有 Azure 订阅，可以在开始前[创建免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* [安装 Docker](service-fabric-mesh-howto-setup-developer-environment-sdk.md#install-docker)

* [在本地安装 Azure CLI 和 Service Fabric 网格 CLI](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli)。

## <a name="create-a-container-registry"></a>创建容器注册表

与 Service Fabric 网格应用程序中的服务关联的容器映像必须存储在容器注册表中。  本教程使用专用 Azure 容器注册表 (ACR) 实例。 

按照下列步骤创建一个 ACR 实例。  如果已有 ACR 实例设置，则可以跳过往前。

### <a name="sign-in-to-azure"></a>登录 Azure

登录到 Azure 并设置活动订阅。

```azurecli
az login
az account set --subscription "<subscriptionName>"
```

### <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 使用以下命令在 eastus 位置创建名为 myResourceGroup 的资源组。

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="create-the-container-registry"></a>创建容器注册表

使用 `az acr create` 命令创建 ACR 实例。 注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 以下示例使用名称 myContainerRegistry  。 如果收到注册表名称正在使用的错误，请选择其他名称。

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry --sku Basic
```

创建注册表时，将看到输出与下面类似：

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-09-13T19:43:57.388203+00:00",
  "id": "/subscriptions/<subscription>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry",
  "location": "eastus",
  "loginServer": "mycontainerregistry.azurecr.io",
  "name": "myContainerRegistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="push-the-images-to-azure-container-registry"></a>向 Azure 容器注册表推送映像

本教程使用待办事项示例应用程序作为示例。  [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) 和 [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) 服务的容器映像可在 Docker 中心找到。 有关如何在 Visual Studio 中构建应用程序的信息，请参阅[构建 Service Fabric 网格 Web 应用](service-fabric-mesh-tutorial-create-dotnetcore.md)。 Service Fabric 网格可以运行 Windows 或 Linux Docker 容器。  如果你使用的是 Linux 容器，请在 Docker 中选择“切换到 Linux 容器”  。  如果你使用的是 Windows 容器，请在 Docker 中选择“切换到 Windows 容器”  。

要将映像推送到 ACR 实例，首先必须具有容器映像。 如果尚不具有任何本地容器映像，请使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令从 Docker 中心拉取 [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) 和 [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) 映像。

拉取 Windows 映像：

```bash
docker pull seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker pull seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

将映像推送到注册表之前，必须使用 ACR 登录服务器的完全限定的名称进行标记。

运行以下命令，获取 ACR 实例的完整登录服务器名称。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table

AcrLoginServer
---------------------------------
mycontainerregistry.azurecr.io
```

现在使用 [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) 命令标记 Docker 映像。 以下示例标记 seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 和 seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 映像。 如果使用不同的映像，请在以下命令中替换这些映像名称。

```bash
docker tag seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker tag seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

登录到 Azure 容器注册表。

```azurecli
az acr login -n myContainerRegistry
```

使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 命令将映像推送到 ACR 实例：

```bash
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

### <a name="list-container-images"></a>列出容器映像

若要查看 ACR 实例中的存储库，请运行以下内容：

```azurecli
az acr repository list --name myContainerRegistry --output table

Result
-------------------------------
seabreeze/azure-mesh-todo-webfrontend
seabreeze/azure-mesh-todo-service
```

以下示例列出了 azure-mesh-todo-service  存储库中的标记。

```azurecli
az acr repository show-tags --name myContainerRegistry --repository seabreeze/azure-mesh-todo-service --output table

Result
--------
1.0-nanoserver-1709
```

上面的输出确认专用容器注册表中存在 `azure-mesh-todo-service:1.0-nanoserver-1709`。  还要验证是否存在 `azure-mesh-todo-webfrontend:1.0-nanoserver-1709`。

## <a name="retrieve-credentials-for-the-registry"></a>检索注册表凭据

> [!IMPORTANT]
> 对于生产方案，建议不要启用 ACR 实例上的管理员用户。 此处这样做是为了方便起见。 对于生产方案，请在生产方案中使用[服务主体](https://docs.microsoft.com/azure/container-registry/container-registry-auth-service-principal)进行用户和系统身份验证。

若要从使用模板创建的注册表中部署容器实例，必须在部署期间提供注册表凭据。 首先，使用以下命令对注册表启用管理员用户：

```azurecli
az acr update --name myContainerRegistry --admin-enabled true
```

接下来，使用以下命令获取注册表登录服务器名称、用户名和密码：

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name myContainerRegistry --query username
az acr credential show --name myContainerRegistry --query "passwords[0].value"
```

在下一节中创建 RM 模板和参数文件时，请使用返回的 ACR 登录服务器名称、用户名和密码值。

## <a name="download-and-explore-the-template-and-parameters-files"></a>下载并浏览模板和参数文件

Service Fabric 网格应用程序是一种 Azure 资源，可以使用 Azure 资源管理器 (RM) 模板进行部署和管理。 如果不熟悉部署和管理 Azure 解决方案的概念，请参阅 [Azure 资源管理器概述](/azure/azure-resource-manager/resource-group-overview)以及[了解 RM 模板的结构和语法](/azure/azure-resource-manager/resource-group-authoring-templates)。

本教程使用待办事项示例作为示例。  下载 [mesh_rp.windows.json 部署模板](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json)和 [mesh_rp.windows.parameter.json 参数](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json)文件，而不是生成新的模板和参数文件。

### <a name="parameters"></a>parameters
当模板中有希望在部署应用程序后进行更改的值，或者希望可以选择在每个部署的基础上进行更改时（如果计划重用此模板进行其他部署），最佳做法是参数化这些值。 执行此操作的正确方法是在部署模板的顶部创建“参数”部分，在其中可以指定参数名称和属性，稍后将在部署模板中引用这些内容。 每个参数定义包括类型、defaultValue和一个带有说明的可选元数据部分。

参数部分在部署模板的顶部定义，就在资源  部分之前：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      ...
    },
    "resources": [
```

在 [mesh_rp.windows.json 部署模板](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json)中，声明了以下参数：location、registryPassword、registryUserName、registryServer、frontEndImage、serviceImage、frontEndCpu、serviceCpu、frontEndMemory、serviceMemory、frontEndReplicaCount、serviceReplicaCount。  可以在部署模板文件中找到每个参数的说明。

这些参数在 [mesh_rp.windows.parameter.json 参数](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json)文件中使用。  使用单独的参数文件，能够在不更新部署模板本身的情况下逐个部署更改参数值。

### <a name="overview-of-the-application-and-services"></a>应用程序和服务概述

服务在模板中指定为应用程序资源的属性。  应用程序部署到专用网络，该网络在模板中声明为资源。  服务可以使用卷来保留数据，这些数据在模板中声明为资源。  对于每项服务，操作系统类型、代码包、副本数量和网络均被指定为服务的属性。  对于每个代码包，请指定容器映像、终结点、内存和 CPU 资源以及映像存储库凭据。 在较高级别，具有多项服务的 Service Fabric 网格应用程序的模板如下所示：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "MyMeshApplication",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/MeshAppNetwork",
        "Microsoft.ServiceFabricMesh/volumes/ServiceAVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "ServiceA",
            "properties": {
              "description": "ServiceA description.",
              "osType": "Linux",
              "codePackages": [
                {
                  "name": "ServiceA",
                  "image": "[parameters('frontEndImage')]",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'ServiceAVolume')]",
                      "destinationPath": "/app/data"
                    }
                  ],
                  "endpoints": [
                    {
                      "name": "ServiceAListener",
                      "port": 80
                    }
                  ],
                  "resources": {
                    "requests": {
                      "cpu": "[parameters('frontEndCpu')]",
                      "memoryInGB": "[parameters('frontEndMemory')]"
                    }
                  },
                  "imageRegistryCredential": {
                    "server": "[parameters('registryServer')]",
                    "username": "[parameters('registryUserName')]",
                    "password": "[parameters('registryPassword')]"
                  }
                }
              ],
              "replicaCount": "[parameters('frontEndReplicaCount')]",
              "networkRefs": [
                {
                  "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'MeshAppNetwork')]"
                }
              ]
            }
          },
          {
            "name": "ServiceB",
            ...
          }
        ],
        "description": "Application description."
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MeshAppNetwork",
      "type": "Microsoft.ServiceFabricMesh/networks",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "MeshAppNetwork description.",
        "addressPrefix": "10.0.0.4/22",
        "ingressConfig": {
          "layer4": [
            {
              "name": "ServiceAIngress",
              "publicPort": "20001",
              "applicationName": "MyMeshApplication",
              "serviceName": "ServiceA",
              "endpointName": "ServiceAListener"
            }
          ]
        }
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "ServiceAVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for counter App.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

有关待办事项应用程序的具体信息，请参阅 [mesh_rp.windows.json 部署模板](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json)文件。

## <a name="deploy-the-application-to-service-fabric-mesh"></a>将应用程序部署到 Service Fabric 网格
使用以下命令创建应用程序和相关资源，并提供先前的[检索注册表凭据](#retrieve-credentials-for-the-registry)步骤中的凭据。

在参数文件中，更新以下参数值：

|参数|值|
|---|---|
|location|要将应用程序部署到的区域。  例如，“eastus”。|
|registryPassword|之前在[检索注册表凭据](#retrieve-credentials-for-the-registry)中获取的密码。 模板中的此参数是安全字符串，不会显示在部署状态或 `az mesh service show` 命令中。|
|registryUserName|在[检索注册表凭据](#retrieve-credentials-for-the-registry)中获取的用户名。|
|registryServer|在[检索注册表凭据](#retrieve-credentials-for-the-registry)中获取的注册表服务器名称。|
|frontEndImage|前端服务的容器映像。  例如，`<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709` 。|
|serviceImage|后端服务的容器映像。  例如，`<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709` 。|

若要部署应用程序，请运行以下内容：

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

此命令将生成如下所示的 JSON 代码片段。 在 JSON 输出的 ```outputs``` 部分下，复制 ```publicIPAddress``` 属性。

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

此信息来自 ARM 模板中的 ```outputs``` 节。 如下所示，此节引用网关资源来获取公共 IP 地址。 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('todolistappGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>打开应用程序

在应用程序成功部署后，获取服务终结点的公共 IP 地址。 部署命令将返回服务终结点的公共 IP 地址。 （可选）还可以通过查询网络资源来查找服务终结点的公共 IP 地址。 此应用程序的网络资源名称是 `todolistappNetwork`，使用以下命令提取与其相关的信息。 

```azurecli
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

在 Web 浏览器中导航到该 IP 地址。

## <a name="check-application-status"></a>检查应用程序状态

可以使用 app show 命令查看应用程序的状态。 已部署的应用程序的应用程序名称是“todolistapp”，请提取其详细信息。

```azurecli
az mesh app show --resource-group myResourceGroup --name todolistapp
```

使用 `az mesh code-package-log get` 命令查看所部署的应用程序的日志：
```azurecli
az mesh code-package-log get --resource-group myResourceGroup --application-name todolistapp --service-name WebFrontEnd --replica-name 0 --code-package-name WebFrontEnd
```

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了如何：

> [!div class="checklist"]
> * 创建专用容器注册表
> * 将容器映像推送到该注册表
> * 创建模板和参数文件
> * 将应用程序部署到 Service Fabric 网格

转到下一教程：
> [!div class="nextstepaction"]
> [扩展 Service Fabric 网格中运行的应用程序](service-fabric-mesh-tutorial-template-scale-services.md)

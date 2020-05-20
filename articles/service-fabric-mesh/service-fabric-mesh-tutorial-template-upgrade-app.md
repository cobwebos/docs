---
title: 教程 - 升级 Azure Service Fabric 网格中运行的应用
description: 本教程介绍如何升级 Azure Service Fabric 网格中运行的应用。
author: dkkapur
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 42db17fa6474d3230bc523d0cf65b375cf01276e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "75351733"
---
# <a name="tutorial-upgrade-a-service-fabric-application-running-in-service-fabric-mesh"></a>教程：升级 Service Fabric 网格中运行的 Service Fabric 应用程序

本教程是一个系列中的第三部分。 本教程介绍如何通过增加分配的 CPU 资源来升级[之前部署到 Service Fabric 网格中的 Service Fabric 应用程序](service-fabric-mesh-tutorial-template-deploy-app.md)。  完成本教程后，你将拥有一个使用更高 CPU 资源运行的 Web 前端服务。

在该系列的第三部分中，你会学习如何：

> [!div class="checklist"]
> * 更改应用程序配置
> * 升级 Service Fabric 网格中的应用程序

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * [使用模板将应用程序部署到 Service Fabric 网格](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [扩展 Service Fabric 网格中运行的应用程序](service-fabric-mesh-tutorial-template-scale-services.md)
> * 升级 Service Fabric 网格中的应用程序
> * [删除应用程序](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 如果没有 Azure 订阅，可以在开始前[创建免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 打开 [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) 或者[在本地安装 Azure CLI 和 Service Fabric 网格 CLI](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli)。

## <a name="upgrade-application-configurations"></a>升级应用程序配置

将应用程序部署到 Service Fabric 网格的一个主要优势是能够轻松地更新应用程序配置。  例如，更新服务的 CPU 或内存资源。

本教程使用“待办事项列表”示例作为示例，该示例[以前已部署](service-fabric-mesh-tutorial-template-deploy-app.md)，现在应该处于运行状态。 此应用程序有两个服务：WebFrontEnd 和 ToDoService。 每项服务在起初部署时的 CPU 资源数为 0.5。  若要查看 WebFrontEnd 服务的 CPU 资源数，请运行以下命令：

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

在应用程序资源的部署模板中，每项服务都有一个 CPU  属性，该属性可以用来设置请求的 CPU 资源数。 应用程序由多项服务组成，每项服务都具有唯一的 CPU  设置，这些服务可以一起进行部署和管理。 为增加 Web 前端服务的 CPU 资源数，请在部署模板或参数文件中修改 cpue 值  。  然后，升级应用程序。

### <a name="modify-the-deployment-template-parameters"></a>修改部署模板参数

当模板中有希望在部署应用程序后进行更改的值，或者希望可以选择在每个部署的基础上进行更改时（如果计划重用此模板进行其他部署），最佳做法是参数化这些值。

以前，应用程序使用 [mesh_rp.windows.json 部署模板](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json)和 [mesh_rp.windows.parameter.json 参数](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json)文件进行部署。

在本地打开 [mesh_rp.windows.parameter.json 参数](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json)文件并将 frontEndCpu 值设置为 1  ：

```json
      "frontEndCpu":{
        "value": "1"
      }
```

保存对参数文件所做的更改。  

frontEndCpu 参数在 [mesh_rp.windows.json 部署模板](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json)的 parameters 节中声明 ：

```json
"frontEndCpu": {
    "defaultValue": "0.5",
    "type": "string",
    "metadata": {
        "description": "The CPU resources for the front end web service."
    }
}
```

WebFrontEnd 服务“codePackages->resources->requests->cpu”属性引用 frontEndCpu 参数   ：

```json
    "services": [
          {
            "name": "WebFrontEnd",
            "properties": {
              "description": "WebFrontEnd description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "WebFrontEnd",
                  "image": "[parameters('frontEndImage')]",
                  ...
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
              ...
```

### <a name="upgrade-your-application"></a>升级应用程序

应用程序正在运行时，可以将其升级，方法是重新部署模板和更新的参数文件：

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

这样就会开始应用程序的滚动升级，你会看到 CPU 资源数在数分钟内增加。  若要查看 WebFrontEnd 服务的 CPU 资源数，请运行以下命令：

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了如何：

> [!div class="checklist"]
> * 更改应用程序配置
> * 升级 Service Fabric 网格中的应用程序

转到下一教程：
> [!div class="nextstepaction"]
> [删除 Service Fabric 网格应用程序](service-fabric-mesh-tutorial-template-remove-app.md)

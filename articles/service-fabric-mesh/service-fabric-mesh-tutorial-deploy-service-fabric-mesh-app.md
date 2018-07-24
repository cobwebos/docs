---
title: 教程 - 将 Service Fabric 网格应用程序部署到 Service Fabric 网格 | Microsoft Docs
description: 了解如何发布 Azure Service Fabric 网格应用程序，该应用程序包括一个可与后端 Web 服务通信的 ASP.NET Core 网站。
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: f9dea759f6556bc521dda4efbd27176f1e06452b
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126569"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-web-application"></a>教程：部署 Service Fabric 网格 Web 应用程序

本教程是一个系列的第三部分，介绍如何直接从 Visual Studio 发布 Azure Service Fabric 网格 Web 应用程序。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 将应用发布到 Azure。
> * 检查应用程序部署状态
> * 查看当前已部署到订阅中的所有应用程序
> * 查看应用程序日志
> * 清理应用使用的资源。

此教程系列介绍了如何：
> [!div class="checklist"]
> * [生成 Service Fabric 网格 Web 应用程序](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [在本地调试应用](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * 将应用发布到 Azure

本教程将介绍如何创建一个包含 ASP.NET Web 前端服务和 ASP.NET Core Web API 后端服务的 Azure Service Fabric 网格应用。 然后，在本地开发群集上调试该应用，并将应用发布到 Azure。 完成本教程后，我们将获得一个简单的待办事项应用，它演示如何在 Azure Service Fabric 网格 Web 应用程序中发出服务到服务调用。

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 如果没有 Azure 订阅，可以在开始前[创建免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 请确保已[设置开发环境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)，包括安装 Service Fabric 运行时、SDK、Docker 和 Visual Studio 2017。

## <a name="download-the-to-do-sample-application"></a>下载待办事项示例应用程序

如果在[本系列教程的第二部分](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)中未生成待办事项示例应用程序，可以下载它。 在命令窗口中，运行以下命令，将示例应用程序存储库克隆到本地计算机。

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

应用程序处于 `src\todolistapp` 目录下。

## <a name="publish-to-azure"></a>发布到 Azure

若要将 Service Fabric 网格项目发布到 Azure，请在 Visual Studio 中右键单击“ServiceFabricMeshApp”并选择“发布...”

接下来，查看“发布 Service Fabric 应用程序”对话框。

![Visual Studio - Service Fabric 网格 - 发布对话框](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

选择自己的 Azure 帐户和订阅。 选择一个**位置**。 本文使用“美国东部”。

在“资源组”下，选择“\<创建新资源组...>”。 此时会出现一个对话框，我们将在其中创建新资源组。 本文使用“美国东部”位置并将组命名为 **sfmeshTutorial1RG**（如果组织有多个人使用同一订阅，请选择唯一的组名称）。  按“创建”以创建资源组，并返回发布对话框。

![Visual Studio - Service Fabric 网格 - 新建资源组对话框](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

返回“发布 Service Fabric 应用程序”对话框，在“Azure 容器注册表”下选择“\<创建新容器注册表...>”。 在“创建容器注册表”对话框中，为“容器注册表名称”使用唯一的名称。 指定一个**位置**（本教程使用“美国东部”）。 在下拉列表中选择在上一步骤创建的**资源组**，例如 **sfmeshTutorial1RG**。 将“SKU”设置为“基本”，然后按“创建”返回发布对话框。

![Visual Studio - Service Fabric 网格 - 新建资源组对话框](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

如果有错误指出尚未为订阅注册资源提供程序，现在可以注册资源提供程序。 首先，查看该资源提供程序是否适用于你的订阅：

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

如果容器注册表提供程序 (`Microsoft.ContainerRegistry`) 可用，请从 Powershell 注册它：

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

在发布对话框中，按“发布”按钮将 Service Fabric 应用程序部署到 Azure。

首次发布到 Azure 时，会将 Docker 映像推送到 Azure 容器注册表 (ACR)，此过程需要一段时间，具体时间取决于映像的大小。 后续发布同一项目会更快。 可以通过在 Visual Studio 的“输出”窗口中选择“Service Fabric 工具”窗格，来监视部署的进度。 完成部署后，“Service Fabric 工具”输出将以 URL 的形式显示应用程序的 IP 地址和端口。

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

打开 Web 浏览器并导航到该 URL，以查看 Azure 中运行的网站。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

可以使用 Azure Cloud Shell 或本地安装的 Azure CLI 来完成剩余的步骤。

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.35 或更高版本。 运行 `az --version` 即可查找版本。 若要安装或升级到最新版本的 CLI，请参阅[安装 Azure CLI 2.0][azure-cli-install]。

## <a name="install-the-az-mesh-cli"></a>安装 az mesh cli
在 CLI 提示符下

1) 删除以前安装的任何 Azure Service Fabric 网格 CLI 模块。

```cli
az extension remove --name mesh
```

2)  安装 Azure Service Fabric 网格 CLI 扩展模块。 在预览版中，Azure Service Fabric 网格 CLI 编写为 Azure CLI 的扩展，但在公共预览版中，它将作为 Azure CLI 的一部分交付。

```cli
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

## <a name="check-application-deployment-status"></a>检查应用程序部署状态

应用程序现已部署。 可以使用 `app show` 命令检查其状态。 

教程应用的应用程序名称为 `ServiceMeshApp`。 使用以下命令收集有关应用程序的详细信息：

```azurecli-interactive
az mesh app show --resource-group $rg --name ServiceMeshApp
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>查看当前已部署到订阅中的所有应用程序

可以使用“app list”命令获取订阅中部署的应用程序的列表。

```cli
az mesh app list --output table
```

## <a name="see-the-application-logs"></a>查看应用程序日志

检查部署的应用程序的日志：

```azurecli-interactive
az mesh code-package-log get --resource-group $rg --application-name ServiceMeshApp --service-name todoservice --replica-name 0 --code-package-name ServiceMeshApp
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要，请删除创建的所有资源。 由于我们已创建一个新资源组来托管 ACR 和 Service Fabric 网格服务资源，因此可以安全删除此资源组，这会删除所有关联的资源。

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

或者，可以[从门户](../azure-resource-manager/resource-group-portal.md#delete-resource-group-or-resources)删除资源组。 

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了：
> [!div class="checklist"]
> * 将应用发布到 Azure。
> * 检查应用程序部署状态
> * 查看当前已部署到订阅中的所有应用程序
> * 查看应用程序日志
> * 清理应用使用的资源。

将 Service Fabric 网格应用程序发布到 Azure 后，请尝试学习以下教程：

* 浏览[投票应用示例](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp)，查看有关服务到服务通信的另一个示例。
* 阅读 [Service Fabric 资源](service-fabric-mesh-service-fabric-resources.md)
* 了解 [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
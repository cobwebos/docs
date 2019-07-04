---
title: 使用 Azure Dev Spaces 在 Kubernetes 上进行 .NET Core 开发 (Visual Studio Code)
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 03/22/2019
ms.topic: quickstart
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s
manager: jeconnoc
ms.openlocfilehash: bab7b4daf8b03115c73b6fbaefe352cecc761b6f
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502997"
---
# <a name="quickstart-develop-with-net-core-on-kubernetes-using-azure-dev-spaces-visual-studio-code"></a>快速入门：使用 Azure Dev Spaces 在 Kubernetes 上进行 .NET Core 开发 (Visual Studio Code)

本指南介绍如何：

- 使用 Azure 中的托管 Kubernetes 群集设置 Azure Dev Spaces。
- 在容器中使用 Visual Studio Code 和命令行以迭代方式开发代码。
- 通过 Visual Studio Code 调试开发空间中的代码。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有帐户，可以[创建一个免费帐户](https://azure.microsoft.com/free)。
- [已安装 Visual Studio Code](https://code.visualstudio.com/download)。
- 已安装适用于 Visual Studio Code 的 [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) 和 [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 扩展。
- [已安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-an-azure-kubernetes-service-cluster"></a>创建 Azure Kubernetes 服务群集

需要在[支持的区域][supported-regions]中创建 AKS 群集。 以下命令创建名为 *MyResourceGroup* 的资源组，以及名为 *MyAKS* 的 AKS 群集。

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>在 AKS 群集上启用 Azure Dev Spaces

使用 `use-dev-spaces` 命令在 AKS 群集上启用 Dev Spaces，然后按提示操作。 以下命令在 *MyResourceGroup* 组中的 *MyAKS* 群集上启用 Dev Spaces，并创建一个默认开发空间。 

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>获取示例应用程序代码

本文使用 [Azure Dev Spaces 示例应用程序](https://github.com/Azure/dev-spaces)来演示 Azure Dev Spaces 的用法。

克隆 GitHub 中的应用程序，并导航到 *dev-spaces/samples/dotnetcore/getting-started/webfrontend* 目录：

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/dotnetcore/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>准备应用程序

使用 `azds prep` 命令生成 Docker 和 Helm 图表资产，以便在 Kubernetes 中运行应用程序：

```cmd
azds prep --public
```

必须在 *dev-spaces/samples/dotnetcore/getting-started/webfrontend* 目录下运行 `prep` 命令才能正确生成 Docker 和 Helm 图表资产。

## <a name="build-and-run-code-in-kubernetes"></a>在 Kubernetes 中生成并运行代码

使用 `azds up` 命令在 AKS 中生成并运行代码：

```cmd
$ azds up
Synchronizing files...4s
Using dev space 'default' with target 'MyAKS'
Installing Helm chart...2s
Waiting for container image build...1m 43s
Building container image...
Step 1/12 : FROM microsoft/dotnet:2.2-sdk
Step 2/12 : ARG BUILD_CONFIGURATION=Debug
Step 3/12 : ENV ASPNETCORE_ENVIRONMENT=Development
Step 4/12 : ENV DOTNET_USE_POLLING_FILE_WATCHER=true
Step 5/12 : EXPOSE 80
Step 6/12 : WORKDIR /src
Step 7/12 : COPY ["webfrontend.csproj", "./"]
Step 8/12 : RUN dotnet restore "webfrontend.csproj"
Step 9/12 : COPY . .
Step 10/12 : RUN dotnet build --no-restore -c $BUILD_CONFIGURATION
Step 11/12 : RUN echo "exec dotnet run --no-build --no-launch-profile -c $BUILD_CONFIGURATION -- \"\$@\"" > /entrypoint.sh
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 3m 44s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

打开 `azds up` 命令输出中显示的公共 URL，可以看到服务正在运行。 在此示例中，公共 URL 为 *http://webfrontend.1234567890abcdef1234.eus.azds.io/* 。

如果使用 *Ctrl+C* 停止 `azds up` 命令，服务将继续在 AKS 中运行，并且公共 URL 仍然可用。

## <a name="update-code"></a>更新代码

若要部署服务的更新版本，可以更新项目中的任何文件，然后重新运行 `azds up` 命令。 例如：

1. 如果 `azds up` 仍在运行，请按 *Ctrl+C*。
1. 将 [`Controllers/HomeController.cs`中的第 20 行](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L20)更新为：
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. 保存所做更改。
1. 重新运行 `azds up` 命令：

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. 导航到正在运行的服务，然后单击“关于”。 
1. 观察所做的更改。
1. 按 *Ctrl+C* 停止 `azds up` 命令。

## <a name="enable-visual-studio-code-to-debug-in-kubernetes"></a>启用 Visual Studio Code 以在 Kubernetes 中进行调试

打开 Visual Studio Code，依次单击“文件”、“打开...”，导航到 *dev-spaces/samples/dotnetcore/getting-started/webfrontend* 目录，然后单击“打开”。   

现在，Visual Studio Code 中会打开 *webfrontend* 项目，这是使用 `azds up` 命令运行的同一个服务。 若要使用 Visual Studio Code 而不是直接使用 `azds up` 在 AKS 中调试此服务，需要准备好此项目，以使用 Visual Studio Code 来与开发空间通信。

若要在 Visual Studio Code 中打开命令面板，请依次单击“视图”、“命令面板”。   开始键入 `Azure Dev Spaces` 并单击 `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`。

![](./media/common/command-palette.png)

此命令会准备好项目，使之直接通过 Visual Studio Code 在 Azure Dev Spaces 中运行。 它还会在项目的根目录下生成包含调试配置的 *.vscode* 目录。

## <a name="build-and-run-code-in-kubernetes-from-visual-studio"></a>通过 Visual Studio 在 Kubernetes 中生成并运行代码

单击左侧的“调试”图标，然后单击顶部的“.NET Core 启动(AZDS)”。  

![](media/get-started-netcore/debug-configuration.png)

此命令会以调试模式在 Azure Dev Spaces 中生成并运行服务。 底部的“终端”窗口会显示运行 Azure Dev Spaces 的服务的生成输出和 URL。  “调试控制台”会显示日志输出。 

> [!Note]
> 如果在“命令面板”中看不到任何 Azure Dev Spaces 命令，请确保已安装[适用于 Azure Dev Spaces 的 Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)。  另请确认是否在 Visual Studio Code 中打开了 *dev-spaces/samples/dotnetcore/getting-started/webfrontend* 目录。

## <a name="setting-and-using-breakpoints-for-debugging"></a>设置并使用用于调试的断点

在调试模式下使用“.NET Core 启动(AZDS)”启动服务。 

依次单击“视图”、“资源管理器”，导航回到“资源管理器”视图。    打开 `Controllers/HomeController.cs` 并单击第 20 行中的某个位置，以将光标置于此处。 若要设置断点，请按 *F9*，或者依次单击“调试”、“切换断点”。  

在浏览器中打开服务，你会发现未显示任何消息。 返回 Visual Studio Code，将会看到，第 20 行已突出显示。 设置的断点在第 20 行处暂停了服务。 若要恢复服务，请按 *F5*，或者依次单击“调试”、“继续”。   返回浏览器，你会发现，现在显示了消息。

在附加调试器的情况下在 Kubernetes 中运行服务时，你对调试信息（例如调用堆栈、局部变量和异常信息）拥有完全访问权限。

将光标置于 `Controllers/HomeController.cs` 中的第 20 行并按 *F9* 以删除断点。

## <a name="update-code-from-visual-studio-code"></a>在 Visual Studio Code 中更新代码

当服务以调试模式运行时，更新 `Controllers/HomeController.cs` 中的第 20 行。 例如：

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

保存文件。 依次单击“调试”、“重新开始调试”，或者在“调试”工具栏中单击“重新开始调试”按钮。    

![](media/common/debug-action-refresh.png)

在浏览器中打开服务，你会发现已显示更新的消息。

Azure Dev Spaces 不会在每次进行代码编辑时都重新生成和重新部署新的容器映像，而是在现有的容器中以增量方式重新编译代码，以加快编辑/调试循环的速度。

## <a name="clean-up-your-azure-resources"></a>清理 Azure 资源

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

了解 Azure Dev Spaces 如何帮助开发跨多个容器的更复杂应用程序，以及如何通过在不同的空间中使用不同的代码版本或分支来简化协作式开发。 

> [!div class="nextstepaction"]
> [使用多个容器和团队开发](multi-service-netcore.md)


[supported-regions]: about.md#supported-regions-and-configurations
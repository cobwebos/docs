---
title: 在 Kubernetes 上开发应用程序
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: 本快速入门演示如何使用 Azure Dev Spaces 和命令行在 Azure Kubernetes 服务中开发应用程序
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 553c316587d27e0921fbbbf78b02ddb048532c43
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867252"
---
# <a name="quickstart-develop-an-application-on-kubernetes---azure-dev-spaces"></a>快速入门：在 Kubernetes 上开发应用程序 - Azure Dev Spaces
本指南介绍如何：

- 使用 Azure 中的托管 Kubernetes 群集设置 Azure Dev Spaces。
- 使用命令行在容器中开发并运行代码。

## <a name="prerequisites"></a>必备条件

- Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
- [已安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-an-azure-kubernetes-service-cluster"></a>创建 Azure Kubernetes 服务群集

需要在[支持的区域][supported-regions]中创建 AKS 群集。 以下命令创建名为 *MyResourceGroup* 的资源组，以及名为 *MyAKS* 的 AKS 群集。

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>在 AKS 群集上启用 Azure Dev Spaces

使用 `use-dev-spaces` 命令在 AKS 群集上启用 Dev Spaces，然后按提示操作。 以下命令在 *MyResourceGroup* 组中的 *MyAKS* 群集上启用 Dev Spaces，并创建一个默认开发空间。 

> [!NOTE]
> `use-dev-spaces` 命令还将安装 Azure Dev Spaces CLI（如果尚未安装）。 无法在 Azure Cloud Shell 中安装 Azure Dev Spaces CLI。

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

克隆 GitHub 中的应用程序，并导航到 *dev-spaces/samples/nodejs/getting-started/webfrontend* 目录：

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>准备应用程序

若要在 Azure Dev Spaces 上运行应用程序，需要 Dockerfile 和 Helm 图表。 对于某些语言，如 [Java][java-quickstart]、 [.NET core][netcore-quickstart] 和 [Node.js][nodejs-quickstart]，Azure Dev Spaces 客户端工具可以生成所需的所有资产。 对于其他许多语言，如 Go、PHP 和 Python，只要你可以提供有效的 Dockerfile，该客户端工具就可以生成 Helm 图表。

使用 `azds prep` 命令生成 Docker 和 Helm 图表资产，以便在 Kubernetes 中运行应用程序：

```cmd
azds prep --public
```

必须在 *dev-spaces/samples/nodejs/getting-started/webfrontend* 目录下运行 `prep` 命令才能正确生成 Docker 和 Helm 图表资产。

> [!TIP]
> `prep` 命令将尝试为项目生成 [Dockerfile 和 Helm 图表](how-dev-spaces-works.md#prepare-your-code)。 Azure Dev Spaces 使用这些文件来生成和运行代码，但是如果要更改项目的生成和运行方式，则可以修改这些文件。

## <a name="build-and-run-code-in-kubernetes"></a>在 Kubernetes 中生成并运行代码

使用 `azds up` 命令在 AKS 中生成并运行代码：

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
Step 1/8 : FROM node
Step 2/8 : ENV PORT 80
Step 3/8 : EXPOSE 80
Step 4/8 : WORKDIR /app
Step 5/8 : COPY package.json .
Step 6/8 : RUN npm install
Step 7/8 : COPY . .
Step 8/8 : CMD ["npm", "start"]
Built container image in 6m 17s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

打开 `azds up` 命令输出中显示的公共 URL，可以看到服务正在运行。 在此示例中，公共 URL 为 *http://webfrontend.1234567890abcdef1234.eus.azds.io/* 。

> [!NOTE]
> 在运行 `azds up` 时导航到服务时，HTTP 请求跟踪也会显示在 `azds up` 命令的输出中。 这些跟踪有助于对服务进行故障排除和调试。 可以在运行 `azds up` 时使用 `--disable-http-traces` 来禁用这些跟踪。

如果使用 *Ctrl+C* 停止 `azds up` 命令，服务将继续在 AKS 中运行，并且公共 URL 仍然可用。

## <a name="update-code"></a>更新代码

若要部署服务的更新版本，可以更新项目中的任何文件，然后重新运行 `azds up` 命令。 例如：

1. 如果 `azds up` 仍在运行，请按 *Ctrl+C*。
1. 将 [`server.js`中的第 13 行](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13)更新为：
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
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

1. 导航到正在运行的服务并观察所做的更改。
1. 按 *Ctrl+C* 停止 `azds up` 命令。

## <a name="clean-up-your-azure-resources"></a>清理 Azure 资源

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

了解 Azure Dev Spaces 如何帮助开发跨多个容器的更复杂应用程序，以及如何通过在不同的空间中使用不同的代码版本或分支来简化协作式开发。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 中的团队开发][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
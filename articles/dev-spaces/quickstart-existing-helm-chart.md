---
title: 使用 Kubernetes 上的现有 Helm 图表开发应用程序
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: 本快速入门展示了如何使用 Azure Dev Spaces 和命令行通过 Azure Kubernetes 服务上的现有 Helm 图表开发应用程序
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s
manager: gwallace
ms.openlocfilehash: c37ea0b04e99cf1bba555e098bdf33b8a8558cfa
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996683"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>快速入门：使用 Kubernetes 上的现有 Helm 图表开发应用程序 - Azure Dev Spaces
本指南介绍如何：

- 使用 Azure 中的托管 Kubernetes 群集设置 Azure Dev Spaces。
- 在命令行上使用 Azure Dev Spaces，通过 AKS 中的现有 Helm 图表运行应用程序。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
- [已安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-an-azure-kubernetes-service-cluster"></a>创建 Azure Kubernetes 服务群集

需要在[支持的区域][supported-regions]中创建 AKS 群集。 以下命令创建名为 *MyResourceGroup* 的资源组，以及名为 *MyAKS* 的 AKS 群集。

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>在 AKS 群集上启用 Azure Dev Spaces

使用 `use-dev-spaces` 命令在 AKS 群集上启用 Dev Spaces，然后按提示操作。 以下命令在 *MyResourceGroup* 组中的 *MyAKS* 群集上启用 Dev Spaces，并创建名为 *dev* 的开发空间。

> [!NOTE]
> `use-dev-spaces` 命令还将安装 Azure Dev Spaces CLI（如果尚未安装）。 无法在 Azure Cloud Shell 中安装 Azure Dev Spaces CLI。

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>获取示例应用程序代码

本文使用 [Azure Dev Spaces 示例应用程序](https://github.com/Azure/dev-spaces)来演示 Azure Dev Spaces 的用法。

从 GitHub 克隆该应用程序，并导航到“dev-spaces/samples/python/getting-started/webfrontend”目录：

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>准备应用程序

若要在 Azure Dev Spaces 上运行应用程序，需要 Dockerfile 和 Helm 图表。 对于某些语言，如 [Java][java-quickstart]、 [.NET core][netcore-quickstart] 和 [Node.js][nodejs-quickstart]，Azure Dev Spaces 客户端工具可以生成所需的所有资产。 对于其他许多语言，如 Go、PHP 和 Python，只要你可以提供有效的 Dockerfile，该客户端工具就可以生成 Helm 图表。 在这种情况下，示例应用程序具有现有的 Dockerfile 和 Helm 图表

使用 `azds prep` 命令，利用现有的 Helm 图表和 Dockerfile 生成用于通过 Azure Dev Spaces 运行应用程序的配置：

```cmd
azds prep --enable-ingress --chart webfrontend/
```

必须从“dev-spaces/samples/python/getting-started/webfrontend”目录运行 `prep` 命令，并使用 `--chart` 指定 Helm 图表的位置。

> [!NOTE]
> 你可能会看到以下警告：*警告：* 由于语言不受支持，无法生成 Dockerfile。 （在运行 `azds prep` 时）。 `azds prep` 命令会尝试为你的项目生成 [Dockerfile 和 Helm 图表](how-dev-spaces-works-prep.md#prepare-your-code)，但不会覆盖任何现有的 Dockerfile 或 Helm 图表。

## <a name="build-and-run-code-in-kubernetes"></a>在 Kubernetes 中生成并运行代码

使用 `azds up` 命令在 AKS 中生成并运行代码：

```cmd
$ azds up
Using dev space 'dev' with target 'MyAKS'
Synchronizing files...14s
Installing Helm chart...2s
Waiting for container image build...3s
Building container image...
Step 1/7 : FROM python:3
Step 2/7 : WORKDIR /python/webfrontend
Step 3/7 : RUN pip install flask
Step 4/7 : COPY webfrontend.py webfrontend.py
Step 5/7 : COPY public/ public/
Step 6/7 : EXPOSE 80
Step 7/7 : CMD ["python", "./webfrontend.py"]
Built container image in 45s
Waiting for container...25s
Service 'azds-543eae-dev-webfrontend' port 'http' is available at http://dev.service.1234567890abcdef1234.eus.azds.io/
Service 'azds-543eae-dev-webfrontend' port 80 (http) is available via port forwarding at http://localhost:52382
Press Ctrl+C to detach
...
```

打开 `azds up` 命令输出中显示的公共 URL，可以看到服务正在运行。 在此示例中，公共 URL 为 `http://dev.service.1234567890abcdef1234.eus.azds.io/`。

> [!NOTE]
> 在运行 `azds up` 时导航到服务时，HTTP 请求跟踪也会显示在 `azds up` 命令的输出中。 这些跟踪有助于对服务进行故障排除和调试。 可以在运行 `azds up` 时使用 `--disable-http-traces` 来禁用这些跟踪。

如果使用 *Ctrl+C* 停止 `azds up` 命令，服务将继续在 AKS 中运行，并且公共 URL 仍然可用。

## <a name="update-code"></a>更新代码

若要部署服务的更新版本，可以更新项目中的任何文件，然后重新运行 `azds up` 命令。 例如：

1. 如果 `azds up` 仍在运行，请按 *Ctrl+C*。
1. 将 [`webfrontend.py`中的第 13 行](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13)更新为：
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. 保存所做更改。
1. 重新运行 `azds up` 命令：

    ```cmd
    $ azds up
    Using dev space 'dev' with target 'MyAKS'
    Synchronizing files...11s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. 导航到正在运行的服务并观察所做的更改。
1. 按 *Ctrl+C* 停止 `azds up` 命令。

## <a name="clean-up-your-azure-resources"></a>清理 Azure 资源

```azurecli
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
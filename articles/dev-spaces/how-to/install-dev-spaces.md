---
title: 在 AKS 上安装 Azure Dev Spaces & 客户端工具
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: 了解如何在 AKS 群集上安装 Azure Dev Spaces，以及如何安装客户端工具。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s
ms.openlocfilehash: 2649b36c96313d4a7d878a1c72c3b175ad0f4d30
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325785"
---
# <a name="install-azure-dev-spaces-on-aks-and-the-client-side-tooling"></a>在 AKS 和客户端工具上安装 Azure Dev Spaces

本文介绍了如何通过多种方式在 AKS 群集上安装 Azure Dev Spaces，以及如何安装客户端工具。

## <a name="install-azure-dev-spaces-using-the-cli"></a>使用 CLI 安装 Azure Dev Spaces

你需要先执行以下操作，然后才能使用 CLI 安装 Dev 空间：
* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户][az-portal-create-account]。
* [Azure CLI 安装][install-cli]。
* [受支持区域][supported-regions]中[的 AKS 群集][create-aks-cli]。

使用 `use-dev-spaces` 命令在 AKS 群集上启用 Dev Spaces，然后按提示操作。

```cmd
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

上述命令在*myResourceGroup*组中的*myAKSCluster*群集上启用 Dev 空间，并创建*默认*的 dev 空间。

```cmd
$ az aks use-dev-spaces -g myResourceGroup -n myAKSCluster

'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

`use-dev-spaces` 命令还会安装 Azure Dev Spaces CLI。

## <a name="install-azure-dev-spaces-using-the-azure-portal"></a>使用 Azure 门户安装 Azure Dev Spaces

你需要先执行以下操作，然后才能使用 Azure 门户安装 Dev 空格：
* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户][az-portal-create-account]。
* [受支持区域][supported-regions]中[的 AKS 群集][create-aks-portal]。

使用 Azure 门户安装 Azure Dev Spaces：
1. 登录到 [Azure 门户][az-portal]。
1. 导航到 AKS 群集。
1. 单击 " *Dev Spaces*"。
1. 将“启用 Dev Spaces”更改为“是”，然后单击“保存”。

![在 Azure 门户中启用 Dev Spaces](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

使用 Azure 门户安装 Azure Dev Spaces 不**会**安装任何用于 Azure Dev Spaces 的客户端工具。

## <a name="install-the-client-side-tooling"></a>安装客户端工具

你可以使用 Azure Dev Spaces 的客户端工具与本地计算机上的 AKS 群集中的 Dev 空间进行交互。 有多种方法来安装客户端工具：

* 在[Visual Studio Code][vscode]中，安装[Azure Dev Spaces 扩展][vscode-extension]。
* 在[Visual Studio 2019][visual-studio]中，安装 Azure 开发工作负荷。
* 在 Visual Studio 2017 中，安装 Web 开发工作负荷并[Visual Studio Tools for Kubernetes][visual-studio-k8s-tools]。
* 下载并安装[Windows][cli-win]、 [Mac][cli-mac]或[Linux][cli-linux] CLI。

## <a name="next-steps"></a>后续步骤

了解 Azure Dev Spaces 如何帮助开发跨多个容器的更复杂应用程序，以及如何通过在不同的空间中使用不同的代码版本或分支来简化协作式开发。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 中的团队开发][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds

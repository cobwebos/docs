---
title: 在 AKS 上启用 Azure 开发空间&安装客户端工具
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: 了解如何在 AKS 群集上启用 Azure 开发空间并安装客户端工具。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s
ms.openlocfilehash: a6b3be5ceba5e60b99b2f75e060f3321cd3151f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898950"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>在 AKS 群集上启用 Azure 开发空间并安装客户端工具

本文介绍在 AKS 群集上启用 Azure 开发空间以及安装客户端工具的几种方法。

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>使用 CLI 启用或删除 Azure 开发空间

在使用 CLI 启用开发空间之前，您需要：
* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户][az-portal-create-account]。
* [已安装 Azure CLI。][install-cli]
* [受支持区域][supported-regions]中的[AKS 群集][create-aks-cli]。

使用 `use-dev-spaces` 命令在 AKS 群集上启用 Dev Spaces，然后按提示操作。

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

上述命令支持*myAAKSCluster 组中的 myAKSCluster 群集*上的开发人员空间，并创建*默认*开发空间。 *myResourceGroup*

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

该`use-dev-spaces`命令还安装 Azure 开发空间 CLI。

要从 AKS 群集中删除 Azure 开发空间`azds remove`，请使用 命令。 例如：

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

上述命令从*MyResourceGroup*中的*MyAKS*群集中删除 Azure 开发空间。 使用 Azure 开发人员空间创建的任何命名空间都将与其工作负荷一起保留，但这些命名空间中的新工作负荷将不会使用 Azure 开发空间进行检测。 此外，如果重新启动使用 Azure 开发人员空间检测的任何现有窗格，可能会看到错误。 在没有 Azure 开发空间工具的情况下，必须重新部署这些窗格。 要从群集中完全删除 Azure 开发人员空间，请删除启用 Azure 开发空间的所有命名空间中的所有窗格。

## <a name="enable-or-remove-azure-dev-spaces-using-the-azure-portal"></a>使用 Azure 门户启用或删除 Azure 开发空间

在使用 Azure 门户启用开发空间之前，需要：
* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户][az-portal-create-account]。
* [受支持区域][supported-regions]中的[AKS 群集][create-aks-portal]。

要使用 Azure 门户启用 Azure 开发空间，请使用以下因素：
1. 登录到 Azure[门户][az-portal]。
1. 导航到 AKS 群集。
1. 选择 *"开发空间"* 菜单项。
1. 将“启用 Dev Spaces”更改为“是”，然后单击“保存”。******

![在 Azure 门户中启用 Dev Spaces](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

使用 Azure 门户启用 Azure 开发空间**不会**为 Azure 开发空间安装任何客户端工具。

要从 AKS 群集中删除 Azure 开发空间，请将*启用开发空间*更改为 *"否*"，然后单击"*保存*"。 使用 Azure 开发人员空间创建的任何命名空间都将与其工作负荷一起保留，但这些命名空间中的新工作负荷将不会使用 Azure 开发空间进行检测。 此外，如果重新启动使用 Azure 开发人员空间检测的任何现有窗格，可能会看到错误。 在没有 Azure 开发空间工具的情况下，必须重新部署这些窗格。 要从群集中完全删除 Azure 开发人员空间，请删除启用 Azure 开发空间的所有命名空间中的所有窗格。

## <a name="install-the-client-side-tools"></a>安装客户端工具

可以使用 Azure 开发人员空间客户端工具从本地计算机与 AKS 群集上的开发空间进行交互。 有几种方法可以安装客户端工具：

* 在[可视化工作室代码][vscode]中，安装[Azure 开发空间扩展][vscode-extension]。
* 在[Visual Studio 2019][visual-studio]中，安装 Azure 开发工作负荷。
* 在 Visual Studio 2017 中，[为 Kubernetes][visual-studio-k8s-tools]安装 Web 开发工作负载和可视化工作室工具。
* 下载并安装[Windows、Mac][cli-win]或[Linux][cli-linux] CLI。 [Mac][cli-mac]

## <a name="next-steps"></a>后续步骤

了解 Azure Dev Spaces 如何帮助开发跨多个容器的更复杂应用程序，以及如何通过在不同的空间中使用不同的代码版本或分支来简化协作式开发。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 中的团队开发][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
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

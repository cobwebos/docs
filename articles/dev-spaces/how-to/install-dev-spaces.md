---
title: 在 AKS 上启用 Azure Dev Spaces 并安装客户端工具
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: 了解如何在 AKS 群集上启用 Azure Dev Spaces 并安装客户端工具。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s
ms.openlocfilehash: 8ebfd8de59ae096e6cee87509bc19e0d161fcaa7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972979"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>在 AKS 群集上启用 Azure Dev Spaces 并安装客户端工具

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

本文介绍了多种方式来在 AKS 群集上启用 Azure Dev Spaces 以及安装客户端工具。

## <a name="enable-azure-dev-spaces-using-the-cli"></a>使用 CLI 启用 Azure Dev Spaces

需具备以下项，才可使用 CLI 启用 Dev Spaces：
* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户][az-portal-create-account]。
* [已安装的 Azure CLI][install-cli]。
* [支持的区域][supported-regions]中的 [AKS 群集][create-aks-cli]。

使用 `use-dev-spaces` 命令在 AKS 群集上启用 Dev Spaces，然后按提示操作。

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

上述命令在 myResourceGroup 组中的 myAKSCluster 群集上启用 Dev Spaces，并创建一个默认开发空间  。

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

`use-dev-spaces` 命令还会安装 Azure Dev Spaces CLI。

## <a name="install-the-client-side-tools"></a>安装客户端工具

可使用 Azure Dev Spaces 客户端工具与本地计算机上 AKS 群集中的开发空间进行交互。 可采用多种方法安装客户端工具：

* 在 [Visual Studio Code][vscode] 中安装 [Azure Dev Spaces 扩展][vscode-extension]。
* 在 [Visual Studio 2019][visual-studio] 中安装 Azure 开发工作负载。
* 下载并安装 [Windows][cli-win]、[Mac][cli-mac] 或 [Linux][cli-linux] CLI。

## <a name="remove-azure-dev-spaces-using-the-cli"></a>使用 CLI 删除 Azure Dev Spaces

要从 AKS 群集中删除 Azure Dev Spaces，请使用 `azds remove` 命令。

```azurecli
azds remove -g MyResourceGroup -n MyAKS
```

以下示例输出显示了从 *MyAKS* 群集中删除 Azure Dev Spaces。

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

使用 Azure Dev Spaces 创建的所有命名空间仍将与其工作负载一起保留，但这些命名空间中新的工作负载将不使用 Azure Dev Spaces 进行检测。 此外，如果你重启已使用 Azure Dev Spaces 检测的任何现有 Pod，可能会看到错误。 必须在没有 Azure Dev Spaces 工具的情况下重新部署这些 Pod。 若要从群集中完全删除 Azure Dev Spaces，请在已启用 Azure Dev Spaces 的所有命名空间中删除所有 Pod。

## <a name="next-steps"></a>后续步骤

深入了解 Azure Dev Spaces 的工作方式。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 工作原理](../how-dev-spaces-works.md)

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds

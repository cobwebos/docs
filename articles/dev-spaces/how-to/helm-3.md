---
title: 将 Azure Dev Spaces 群集配置为使用 Helm 3 （预览版）
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: 了解如何配置 Dev Spaces 群集以使用 Helm 3
keywords: Azure Dev Spaces，Dev 空间，Docker，Kubernetes，Azure，AKS，Azure Kubernetes 服务，容器
ms.openlocfilehash: 9e3f3ff90d36215c386bf1d8b8ec1edd54ebfb6a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202255"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>将 Azure Dev Spaces 群集配置为使用 Helm 3 （预览版）

默认情况下，Azure Dev Spaces 使用 Helm 2 在 AKS 群集上的开发共享空间中安装用户服务。 可以启用 Azure Dev Spaces 使用 Helm 3 而不是 Helm 2 在 Dev 空间中安装用户服务。 无论使用何种版本的 Helm Azure Dev Spaces 用于安装用户服务，可以继续使用 Helm 2 或3客户端在同一群集上管理自己的版本。

启用 Helm 3 时，Azure Dev Spaces 的行为方式与通过以下方式在开发人员共享空间中安装用户服务的行为不同：

* Tiller 不再部署到*azds*命名空间中的群集。
* Helm 将版本信息存储在安装了服务的命名空间中，而不是*azds*命名空间。
* Helm 3 版本信息保留在删除控制器后安装服务的命名空间中。
* 你可以使用 Helm 3 客户端直接与群集上通过 Azure Dev Spaces 管理的任何发布交互。

在本指南中，你将了解如何启用 Helm 3，以便 Azure Dev Spaces 在 Dev 空间中安装用户服务。

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="before-you-begin"></a>开始之前

### <a name="prerequisites"></a>必备条件

* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
* [已安装 Azure CLI][azure-cli]。

### <a name="register-the-helm3preview-preview-feature"></a>注册 Helm3Preview 预览功能

若要启用 Azure Dev Spaces 使用 Helm 3 在 Dev 空间中安装用户服务，请先使用*az feature register*命令在订阅上启用*Helm3Preview*功能标志：

> [!WARNING]
> 使用*Helm3Preview*功能标志 Azure Dev Spaces 启用的任何 AKS 群集都将使用此预览版体验。 若要继续在 AKS 群集上启用完全受支持的 Azure Dev Spaces，请不要对生产订阅启用预览功能。 使用单独的测试或开发 Azure 订阅来测试预览功能。

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

完成注册需要几分钟时间。 使用*az feature show*命令检查注册状态：

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

*注册* *状态*后，使用*az provider register*刷新*DevSpaces*的注册：

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>限制

此功能处于预览阶段时有以下限制：

* 不能在具有现有工作负荷的 AKS 群集上使用此功能。 必须创建新的 AKS 群集。

## <a name="create-your-cluster"></a>创建群集

在具有此预览功能的区域中创建新的 AKS 群集。 以下命令在*美国中南部*区域创建名为*MyResourceGroup*的资源组和名为*MyAKS*的新 AKS 群集：

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>启用 Azure Dev Spaces

使用 "*使用*AKS" 命令在你的群集上启用 dev 空间，并按照提示进行操作。 以下命令在*MyResourceGroup*组中的*MyAKS*群集上启用 Dev 空间，并创建默认的 dev 空间。

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>验证 Dev 空格是否正在运行 Helm 3

在*azds*命名空间中列出部署，验证 tiller 是否未运行：

```cmd
kubectl get deployment -n azds
```

确认*tiller*未在 azds 命名空间中运行。 例如：

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>后续步骤

了解 Azure Dev Spaces 如何帮助开发跨多个容器的更复杂应用程序，以及如何通过在不同的空间中使用不同的代码版本或分支来简化协作式开发。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 中的团队开发][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md
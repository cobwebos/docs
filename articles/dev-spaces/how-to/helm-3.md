---
title: 将 Azure 开发人员空间群集配置为使用 Helm 3（预览版）
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: 了解如何将开发人员空间群集配置为使用 Helm 3
keywords: Azure 开发空间、开发空间、Docker、库伯奈斯、Azure、AKS、Azure 库伯奈斯服务、容器
ms.openlocfilehash: dbccb2618fd5a27805261d60e7891d920e0bc372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454289"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>将 Azure 开发人员空间群集配置为使用 Helm 3（预览版）

默认情况下，Azure 开发空间使用 Helm 2 在 AKS 群集上的开发空间中安装用户服务。 您可以使 Azure 开发人员空间使用 Helm 3，而不是 Helm 2 在开发空间中安装用户服务。 无论 Helm Azure 开发人员空间用于安装用户服务的版本如何，您都可以继续使用 Helm 2 或 3 客户端管理同一群集上自己的版本。

启用 Helm 3 时，Azure 开发人员空间在开发空间中以下列方式安装用户服务时的行为方式不同：

* 蒂勒不再部署在*azds*命名空间中的群集。
* Helm 在安装服务的命名空间中存储发布信息，而不是*azds*命名空间。
* Helm 3 发布信息将保留在删除控制器后安装服务的命名空间中。
* 您可以使用 Helm 3 客户端直接与群集上的 Azure 开发人员空间管理的任何版本进行交互。

在本指南中，您将了解如何启用 Azure 开发人员空间的 Helm 3，以便在开发空间中安装用户服务。

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="before-you-begin"></a>开始之前

### <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
* [已安装 Azure CLI][azure-cli]。

### <a name="register-the-helm3preview-preview-feature"></a>注册 Helm3 预览预览功能

要使 Azure 开发人员空间使用 Helm 3 在开发空间中安装用户服务，请首先使用*az 功能寄存器*命令在订阅上启用*Helm3 预览*功能标志：

> [!WARNING]
> 使用*Helm3 预览*功能标志启用 Azure 开发空间的任何 AKS 群集都将使用此预览体验。 要继续在 AKS 群集上启用完全支持的 Azure 开发空间，请不要在生产订阅上启用预览功能。 使用单独的测试或开发 Azure 订阅来测试预览功能。

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

完成注册需要几分钟时间。 使用*az 要素显示*命令检查注册状态：

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

注册*状态**时，请*刷新*Microsoft.DevSpaces*使用*az 提供程序注册*：

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>限制

此功能处于预览版时，适用以下限制：

* 不能在具有现有工作负载的 AKS 群集上使用此功能。 您必须创建新的 AKS 群集。

## <a name="create-your-cluster"></a>创建群集

在具有此预览功能的区域创建新的 AKS 群集。 以下命令创建名为*MyResourceGroup*的资源组和名为*MyAKS*的新 AKS 群集：

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>启用 Azure 开发空间

使用*使用-开发空间*命令在 AKS 群集上启用开发空间，并按照提示操作。 以下命令在 *MyResourceGroup* 组中的 *MyAKS* 群集上启用 Dev Spaces，并创建一个默认开发空间。

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>验证开发空间正在运行头盔 3

通过在*azds*命名空间中列出部署来验证收银台未运行：

```cmd
kubectl get deployment -n azds
```

确认在 azds 命名空间中未运行*耕种器部署*。 例如：

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
---
title: Azure Dev Spaces 的业务连续性和灾难恢复
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
manager: jeconnoc
ms.openlocfilehash: 69f5bdd80e4cf10db6a530ddfa08a1f26cd42ca0
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754414"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Azure Dev Spaces 的业务连续性和灾难恢复

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>查看 Azure Kubernetes 服务 (AKS) 的灾难恢复指南

Azure Dev Spaces 是 Azure Kubernetes 服务 (AKS) 的一项功能。 应了解 AKS 中的灾难恢复指南，并考虑它们是否适用于用作 Dev Spaces 的 AKS 群集。 有关详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 中实现业务连续性和灾难恢复的最佳做法](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>在不同区域的 AKS 群集上启用 Dev Spaces

通过在不同区域的 AKS 群集上启用 Dev Spaces，可以在 Azure 区域发生故障后立即恢复使用 Dev Spaces。

有关 AKS 的多区域部署的常规信息，请参阅[规划多区域部署](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment)

有关部署与 Azure Dev Spaces 兼容的 AKS 群集的信息，请参阅[使用 Azure Cloud Shell 创建 Kubernetes 群集](https://docs.microsoft.com/azure/dev-spaces/how-to/create-cluster-cloud-shell)

### <a name="enable-dev-spaces-via-the-azure-portal"></a>通过 Azure 门户启用 Dev Spaces

单击 Azure 门户中每个群集属性下的“Dev Spaces”  导航项。 然后选择该选项以启用 Dev Spaces。

![通过 Azure 门户启用 Dev Spaces](../media/common/enable-dev-spaces.jpg)

对每个群集重复此过程。

### <a name="enable-dev-spaces-via-the-azure-cli"></a>通过 Azure CLI 启用 Dev Spaces

此外，还可以通过命令行启用 Dev Spaces：

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>将团队基线部署到每个群集

在使用 Dev Spaces 时，通常将整个应用程序部署到 Kubernetes 群集上的父开发空间。 默认使用 `default` 空间。 初始部署包括所有服务，以及这些服务所依赖的外部资源，例如数据库或队列。 这就是所谓的“基线”  。 在父开发空间中设置基线后，就可以在子开发空间中迭代和调试各个服务。

应将最新版本的服务基线集部署到多个区域中的群集。 以此方式更新基线服务可确保在 Azure 区域出现故障的情况下继续使用 Dev Space。 例如，如果通过 CI/CD 管道部署基线，请修改管道，使其部署到不同区域的多个群集。

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>选择用于 Dev Spaces 的正确 AKS 群集

一旦正确配置运行团队基线的备份群集，就可以随时快速切换到备份群集。 然后可以在 Dev Spaces 中重新运行正在处理的各个服务。

使用以下 CLI 命令选择不同的群集：

```cmd
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

可使用以下命令列出新群集上的可用开发空间：

```cmd
azds space list
```

可以使用以下命令创建要使用的新开发空间，或者选择一个现有的开发空间：

```cmd
azds space select -n <space name>
```

在运行这些命令后，所选群集和开发空间将用于进行后续 CLI 操作，并用于通过 Azure Dev Spaces 的 Visual Studio Code 扩展来调试项目。

如果使用的是 Visual Studio，则可以通过以下步骤切换现有项目使用的群集：

1. 在 Visual Studio 中打开项目。
1. 在解决方案资源管理器中右键单击项目名称，然后单击“属性” 
1. 在左窗格中，单击“调试” 
1. 在“调试属性”页上，单击“配置文件”  下拉列表，然后选择“Azure Dev Spaces”  。
1. 单击“更改”  按钮。
1. 在出现的对话框中，选择想要使用的 AKS 群集。 如果需要，请选择要使用的不同开发空间，或者创建新的开发空间，具体通过从“空间”  下拉列表选择相应选项来创建。

选择正确群集和空间后，可以按 F5 在 Dev Spaces 中运行服务。

对任何配置为使用原始群集的其他项目重复这些步骤。

## <a name="access-a-service-on-a-backup-cluster"></a>访问备份群集上的服务

如果已将服务配置为使用公共 DNS 名称，那么在备份群集上运行该服务时，它将具有不同的 URL。 公共 DNS 名称始终采用 `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io` 格式。 如果切换到不同群集，群集 GUID 和区域可能会发生变化。

在运行 `azds up` 时，或者在 Visual Studio“Azure Dev Spaces”  下的“输出”窗口中，Dev Spaces 始终显示正确的服务 URL。

此外可以通过运行 `azds list-uris` 命令找到该 URL：
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

请在访问服务时使用此 URL。

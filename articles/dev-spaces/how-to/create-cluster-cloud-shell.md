---
title: 创建 Azure Dev Spaces 启用了 Azure Cloud Shell 的 Kubernetes 群集
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: 了解如何直接从浏览器快速创建为 Azure Dev Spaces 启用的 Kubernetes 群集，而无需安装任何内容。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s
ms.openlocfilehash: ac825caa7aacbc6ac21dd1f9fe7acbb9cf12e900
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325759"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>使用 Azure Cloud Shell 启用 Azure Dev Spaces 创建 Kubernetes 群集

通过使用此页中的 "**试用**" 按钮，可以使用[Azure Cloud Shell](/azure/cloud-shell)创建 Azure Kubernetes Service 群集。 如果未登录，请按照提示使用 Azure 帐户登录，然后在出现 Azure Cloud Shell 提示符时在 Azure Cloud Shell 提示符下键入命令。

## <a name="create-the-cluster"></a>创建群集

首先，在[支持 Azure Dev Spaces 的区域][supported-regions]中创建资源组。

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

使用以下命令创建 Kubernetes 群集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

创建群集需要几分钟时间。  完成后，输出以 JSON 格式显示。 查找 `provisioningState` 并验证它是否为 `Succeeded`。

## <a name="next-steps"></a>后续步骤

如需完整教程的链接，请参阅 [Azure Dev Spaces](/azure/dev-spaces/)。

> [!IMPORTANT]
> 许多 Azure Dev Spaces 快速入门和教程都使用 Azure Dev Spaces CLI 来执行操作。 无法在 Azure Cloud Shell 中安装 Azure Dev Spaces CLI。


[supported-regions]: ../about.md#supported-regions-and-configurations
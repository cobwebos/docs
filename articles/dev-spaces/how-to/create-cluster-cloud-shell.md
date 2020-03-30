---
title: 创建启用 Azure 开发空间的 Kubernetes 群集 - Azure 云外壳
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: 了解如何直接从浏览器快速创建为 Azure Dev Spaces 启用的 Kubernetes 群集，而无需安装任何内容。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s
ms.openlocfilehash: 5e2e5cfd22eeedd3554737458caeca0b891b62fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605301"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>使用 Azure 云外壳启用 Azure 开发空间创建库伯奈斯群集

您可以使用[Azure 云外壳](/azure/cloud-shell)使用此页面中的"**试用"** 按钮创建 Azure 库伯奈斯服务群集。 如果未登录，请按照提示使用 Azure 帐户登录，然后在出现 Azure Cloud Shell 提示符时在 Azure Cloud Shell 提示符下键入命令。

## <a name="create-the-cluster"></a>创建群集

首先，在[支持 Azure 开发空间的区域][supported-regions]中创建资源组。

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

使用以下命令创建 Kubernetes 群集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

创建群集需要几分钟时间。  完成后，输出以 JSON 格式显示。 查找 `provisioningState` 并验证它是否为 `Succeeded`。

## <a name="next-steps"></a>后续步骤

如需完整教程的链接，请参阅 [Azure Dev Spaces](/azure/dev-spaces/)。

> [!IMPORTANT]
> 许多 Azure 开发空间快速入门和教程使用 Azure 开发空间 CLI 执行操作。 无法在 Azure Cloud Shell 中安装 Azure Dev Spaces CLI。


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
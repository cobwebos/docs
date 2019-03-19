---
title: 如何创建启用 Azure 开发人员空间使用 Azure Cloud Shell 的 Kubernetes 群集
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: conceptual
description: 了解如何直接从浏览器快速创建为 Azure Dev Spaces 启用的 Kubernetes 群集，而无需安装任何内容。
keywords: Docker，Kubernetes，Azure，AKS，Azure Kubernetes 服务，容器，Helm，服务网格，服务网格路由、 kubectl，k8s
ms.openlocfilehash: bfde055c99a1109a8f71c838b44ed1742e3f06aa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895560"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>使用 Azure Cloud Shell 创建 Kubernetes 群集

可以使用 [Azure Cloud Shell](/azure/cloud-shell) 通过使用此页中的“试用”按钮为 Azure Dev Spaces 创建群集。 如果未登录，请按照提示使用 Azure 帐户登录，然后在出现 Azure Cloud Shell 提示符时在 Azure Cloud Shell 提示符下键入命令。

## <a name="create-the-cluster"></a>创建群集

首先，在其中创建资源组[支持 Azure 开发人员空间区域](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams)。

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

使用以下命令创建 Kubernetes 群集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region>
```

创建群集需要几分钟时间。  完成后，输出以 JSON 格式显示。 查找 `provisioningState` 并验证它是否为 `Succeeded`。

## <a name="next-steps"></a>后续步骤

如需完整教程的链接，请参阅 [Azure Dev Spaces](/azure/dev-spaces/)。

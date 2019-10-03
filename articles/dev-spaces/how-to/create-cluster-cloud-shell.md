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
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s
ms.openlocfilehash: cd0c8c3c26feefe3448ada1cf1575706cd17e525
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808690"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>使用 Azure Cloud Shell 创建 Kubernetes 群集

可以使用[Azure Cloud Shell](/azure/cloud-shell)若要使用创建的 Azure Kubernetes 服务群集**试用**从此页的按钮。 如果未登录，请按照提示使用 Azure 帐户登录，然后在出现 Azure Cloud Shell 提示符时在 Azure Cloud Shell 提示符下键入命令。

## <a name="create-the-cluster"></a>创建群集

首先，在其中创建资源组[支持 Azure 开发人员空间的区域][supported-regions]。

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
> 许多 Azure 开发人员空间快速入门和教程使用 Azure 开发人员空格 CLI 来执行操作。 不能在 Azure Cloud Shell 中安装 Azure 开发人员空格 CLI。


[supported-regions]: ../about.md#supported-regions-and-configurations
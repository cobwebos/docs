---
title: 如何将 kubectl 与 Azure Dev Spaces 配合使用
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: 了解如何在启用 Azure Dev Spaces 的 Azure Kubernetes Service 群集上的开发环境中使用 kubectl 命令
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
ms.openlocfilehash: e6f79d98cf209d1bc19753f19c9b17b06017c2b7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91960148"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>将 kubectl 与 Azure Dev Space 配合使用

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

可以在 Azure Dev Space 中访问 Kubernetes 群集，并使用现有的 Kubernetes 工具（如 `kubectl`）。

运行 `az aks use-dev-spaces` 命令会自动添加 `kubectl` 配置上下文，因此 kubectl 应已连接到 Azure Dev Spaces Kubernetes 群集。 示例：
- 确认当前上下文：`kubectl config current-context`
- 列出所有可用的上下文：`kubectl config get-contexts`。 
- 更改上下文：`kubectl config use-context <context-name>`
- 查看 Kubernetes 仪表板：运行 `kubectl proxy`，然后将浏览器打开到该命令发出的地址（将 `/ui` 追加到 URL 后以导航到 Kubernetes 仪表板）。
- 列出默认 Azure Dev Spaces 空间中名为 *default*的正在运行的服务： `kubectl get services --namespace=default`


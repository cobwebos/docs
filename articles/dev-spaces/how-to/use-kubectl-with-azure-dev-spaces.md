---
title: 如何使用库布克特尔与 Azure 开发空间
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: 了解如何在启用 Azure 开发人员空间的 Azure Kubernetes 服务群集上的开发空间中使用 kubectl 命令
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438367"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>将 kubectl 与 Azure Dev Space 配合使用

可以在 Azure Dev Space 中访问 Kubernetes 群集，并使用现有的 Kubernetes 工具（如 `kubectl`）。

运行 `az aks use-dev-spaces` 命令会自动添加 `kubectl` 配置上下文，因此 kubectl 应已连接到 Azure Dev Spaces Kubernetes 群集。 示例：
- 确认当前上下文：`kubectl config current-context`
- 列出所有可用的上下文：`kubectl config get-contexts`。 
- 更改上下文：`kubectl config use-context <context-name>`
- 查看 Kubernetes 仪表板：运行 `kubectl proxy`，然后将浏览器打开到该命令发出的地址（将 `/ui` 追加到 URL 后以导航到 Kubernetes 仪表板）。
- 在默认的 Azure 开发人员空间中列出名为*默认*的服务：`kubectl get services --namespace=default`


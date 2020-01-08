---
title: 如何将 kubectl 与 Azure Dev Spaces 配合使用
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: 了解如何在启用 Azure Dev Spaces 的 Azure Kubernetes Service 群集上的开发环境中使用 kubectl 命令
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438367"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>将 kubectl 与 Azure Dev Space 配合使用

可以在 Azure Dev Space 中访问 Kubernetes 群集，并使用现有的 Kubernetes 工具（如 `kubectl`）。

运行 `az aks use-dev-spaces` 命令会自动添加 `kubectl` 配置上下文，因此 kubectl 应已连接到 Azure Dev Spaces Kubernetes 群集。 示例：
- 确认当前上下文：`kubectl config current-context`
- 列出所有可用的上下文：`kubectl config get-contexts`。 
- 更改上下文：`kubectl config use-context <context-name>`
- 查看 Kubernetes 仪表板：运行 `kubectl proxy`，然后将浏览器打开到该命令发出的地址（将 `/ui` 追加到 URL 后以导航到 Kubernetes 仪表板）。
- 在名为 *default* 的默认 Azure Dev Spaces 空间中列出正在运行的服务：`kubectl get services --namespace=default`


---
title: 如何使用 Azure 开发人员空间使用 kubectl
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: 'Docker，Kubernetes，Azure，AKS，Azure Kubernetes 服务，容器，Helm，服务网格，服务网格路由、 kubectl，k8s '
ms.openlocfilehash: 0dfe88966deeb4dcf0196aa1f1584a06794b36a7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773259"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>将 kubectl 与 Azure Dev Space 配合使用

可以在 Azure Dev Space 中访问 Kubernetes 群集，并使用现有的 Kubernetes 工具（如 `kubectl`）。

运行 `az aks use-dev-spaces` 命令会自动添加 `kubectl` 配置上下文，因此 kubectl 应已连接到 Azure Dev Spaces Kubernetes 群集。 示例：
- 确认当前上下文：`kubectl config current-context`
- 列出所有可用的上下文：`kubectl config get-contexts`。 
- 更改上下文：`kubectl config use-context <context-name>`
- 查看 Kubernetes 仪表板：运行 `kubectl proxy`，然后将浏览器打开到该命令发出的地址（将 `/ui` 追加到 URL 后以导航到 Kubernetes 仪表板）。
- 在名为 *default* 的默认 Azure Dev Spaces 空间中列出正在运行的服务：`kubectl get services --namespace=default`


---
title: 如何将 kubectl 与 Azure Dev Spaces 配合使用 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: article
description: 使用 Azure 上的容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器
ms.openlocfilehash: b0ceccc4f8b16c21e8a66a5f1b8cf0e7b6f47a4f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469305"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>将 kubectl 与 Azure Dev Space 配合使用

可以在 Azure Dev Space 中访问 Kubernetes 群集，并使用现有的 Kubernetes 工具（如 `kubectl`）。

运行 `az aks use-dev-spaces` 命令会自动添加 `kubectl` 配置上下文，因此 kubectl 应已连接到 Azure Dev Spaces Kubernetes 群集。 示例：
- 确认当前上下文：`kubectl config current-context`
- 列出所有可用的上下文：`kubectl config get-contexts`。 
- 更改上下文：`kubectl config use-context <context-name>`
- 查看 Kubernetes 仪表板：运行 `kubectl proxy`，然后将浏览器打开到该命令发出的地址（将 `/ui` 追加到 URL 后以导航到 Kubernetes 仪表板）。
- 在名为 *default* 的默认 Azure Dev Spaces 空间中列出正在运行的服务：`kubectl get services --namespace=default`


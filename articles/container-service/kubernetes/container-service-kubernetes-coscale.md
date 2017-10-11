---
title: "使用 CoScale 监视 Azure Kubernetes 群集 | Microsoft Docs"
description: "使用 CoScale 在 Azure 容器服务中监视 Kubernetes 群集"
services: container-service
documentationcenter: 
author: fryckbos
manager: 
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f894191baced710fc0f5a8c8692df98033341a48
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>使用 CoScale 监视 Azure 容器服务 Kubernetes 群集

在本文中，我们将介绍如何部署 [CoScale](https://www.coscale.com/) 代理以监视 Azure 容器服务中 Kubernetes 群集中的所有节点和容器。 此配置需要具有 CoScale 的帐户。 


## <a name="about-coscale"></a>关于 CoScale 

CoScale 是一种监视平台，用于从多个业务流程平台中的所有容器中收集指标和事件。 CoScale 提供针对 Kubernetes 环境的完整堆栈监视。 它提供针对堆栈中所有层的可视化和分析：OS、Kubernetes、Docker 和在容器内运行的应用程序。 CoScale 提供几个内置监视仪表板，且具有内置异常检测，以允许操作人员和开发人员快速发现基础结构和应用程序问题。

![CoScale UI](./media/container-service-kubernetes-coscale/coscale.png)

如本文中所示，可以在 Kubernetes 群集上安装代理以将 CoScale 作为 SaaS 解决方案运行。 如果想要就地保留数据，CoScale 还可用于本地安装。


## <a name="prerequisites"></a>先决条件

首先需要[创建 CoScale 帐户](https://www.coscale.com/free-trial)。

本演练假定用户已[使用 Azure 容器服务创建 Kubernetes 群集](container-service-kubernetes-walkthrough.md)，

并假设已安装 `az` Azure CLI 和 `kubectl` 工具。

可以通过运行以下语句测试是否已安装 `az` 工具：

```azurecli
az --version
```

如果尚未安装 `az` 工具，请参阅[此处](/cli/azure/install-azure-cli)的说明。

可以通过运行以下语句测试是否已安装 `kubectl` 工具：

```bash
kubectl version
```

如果尚未安装 `kubectl`，则可运行：

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>使用 DaemonSet 安装 CoScale 代理
Kubernetes 使用 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) 在群集中的每个主机上运行一个容器实例。
DaemonSet 还特别适合用于运行监视代理，例如 CoScale 代理。

登录到 CoScale 后，请转到[代理页](https://app.coscale.com/)，使用 DaemonSet 在群集上安装 CoScale 代理。 CoScale UI 提供引导式配置步骤，用于创建代理并开始监视完整的 Kubernetes 群集。

![CoScale 代理配置](./media/container-service-kubernetes-coscale/installation.png)

若要在群集上启动代理，请运行提供的命令：

![启动 CoScale 代理](./media/container-service-kubernetes-coscale/agent_script.png)

就这么简单！ 代理安装完毕并开始运行后，几分钟内即可在控制台中查看数据。 请访问[代理页](https://app.coscale.com/)，以查看群集的摘要、执行其他配置步骤并查看“Kubernetes 群集概述”等仪表板。

![Kubernetes 群集概述](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

CoScale 代理将自动部署在群集中的新计算机上。 发布新版本时，代理将自动更新。


## <a name="next-steps"></a>后续步骤

有关 CoScale 监视解决方案的详细信息，请参阅 [CoScale 文档](http://docs.coscale.com/)和[博客](https://www.coscale.com/blog)。 


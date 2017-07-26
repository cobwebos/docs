---
title: "使用 DataDog 监视 Azure Kubernetes 群集 | Microsoft 文档"
description: "使用 DataDog 在 Azure 容器服务中监视 Kubernetes 群集"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: what-goes-here?
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: f7377b3d0c0b89d7e196a31890173575688687cf
ms.openlocfilehash: 901cbf5093c6a547f5dffa7ed6d71fe67caaadb9
ms.lasthandoff: 03/02/2017


---

# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>通过 DataDog 监视 Azure 容器服务群集

## <a name="prerequisites"></a>先决条件
本演练假定用户已[使用 Azure 容器服务创建 Kubernetes 群集](container-service-kubernetes-walkthrough.md)，

并假设已安装 `az` Azure CLI 和 `kubectl` 工具。

可以通过运行以下语句测试是否已安装 `az` 工具：

```console
$ az --version
```

如果尚未安装 `az` 工具，请参阅[此处](https://github.com/azure/azure-cli#installation)的说明。

可以通过运行以下语句测试是否已安装 `kubectl` 工具：

```console
$ kubectl version
```

如果尚未安装 `kubectl`，则可运行：

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog
Datadog 是一种从 Azure 容器服务群集中的容器收集监视数据的监视服务。 Datadog 具有 Docker 集成仪表板，可通过它查看容器中的特定指标。 从容器中收集的指标由 CPU、内存、网络和 I/O 组织。 Datadog 将指标拆分为容器和映像。

首先需要[创建帐户](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>使用 DaemonSet 安装 DataDog 代理
Kubernetes 使用 DaemonSet 在群集中的每个主机上运行一个容器实例。
DaemonSet 还特别适合用于运行监视代理。

登录到 DataDog 后，可按照 [DataDog 说明](https://app.datadoghq.com/account/settings#agent/kubernetes)使用 DaemonSet 在群集上安装 DataDog 代理。

## <a name="conclusion"></a>结束语
就这么简单！ 代理安装完毕并开始运行后，几分钟内即可在控制台中查看数据。 可访问集成 [kubernetes 仪表板](https://app.datadoghq.com/screen/integration/kubernetes)以查看群集的摘要。


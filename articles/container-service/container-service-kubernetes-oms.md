---
title: "监视 Azure Kubernetes 群集 - 操作管理 | Microsoft Docs"
description: "使用 Microsoft Operations Management Suite 监视 Azure 容器服务中的 Kubernetes 群集"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: 4e4a4f4e299dc2747eb48bbd2e064cd80783211c
ms.openlocfilehash: 46240f3dc99a8c8a103a1e7919ad4f5e7a8ea62a
ms.lasthandoff: 04/04/2017


---

# <a name="monitor-an-azure-container-service-cluster-with-microsoft-operations-management-suite-oms"></a>使用 Microsoft Operations Management Suite (OMS) 监视 Azure 容器服务群集

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

## <a name="monitoring-containers-with-operations-management-suite-oms"></a>使用 Microsoft Operations Management Suite (OMS) 监视容器

Microsoft Operations Management (OMS) 是 Microsoft 基于云的 IT 管理解决方案，有助于管理和保护本地和云基础结构。 容器解决方案是 OMS Log Analytics 中的一种解决方案，有助于查看单个位置中的容器库存、性能和日志。 通过查看集中位置中的日志，可以审核、排查容器问题，并查找主机上干扰性消耗过多的容器。

![](media/container-service-monitoring-oms/image1.png)

有关容器解决方案的详细信息，请参阅[容器解决方案 Log Analytics](../log-analytics/log-analytics-containers.md)。

## <a name="installing-oms-on-kubernetes"></a>在 Kubernetes 上安装 OMS

### <a name="obtain-your-workspace-id-and-key"></a>获取工作区 ID 和密钥
为了使 OMS 代理与服务通信，需要为其配置工作区 ID 和工作区密钥。 若要获取工作区 ID 和密钥，需在 <https://mms.microsoft.com> 创建 OMS 帐户。
请按照步骤创建帐户。 帐户创建完成后，依次单击“设置”、“连接源”和“Linux 服务器”，获取工作区 ID 和密钥，如下所示。

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-oms-agent-using-a-daemonset"></a>使用 DaemonSet 安装 OMS 代理
Kubernetes 使用 DaemonSet 在群集中的每个主机上运行一个容器实例。
DaemonSet 还特别适合用于运行监视代理。

以下是 [DaemonSet YAML 文件](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)。 将其保存到名为 `oms-daemonset.yaml` 的文件，在文件中将 `WSID` 和 `KEY` 的占位符值分别替换为工作区 ID 和密钥。

将工作区 ID 和密钥添加到 DaemonSet 配置中后，可以使用 `kubectl` 命令行工具在群集上安装 OMS 代理：

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="conclusion"></a>结束语
就这么简单！ 几分钟后，应该可以看到数据流向 OMS 仪表板。


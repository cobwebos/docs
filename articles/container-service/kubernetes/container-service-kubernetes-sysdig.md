---
title: "监视 Azure Kubernetes 群集 - Sysdig | Microsoft 文档"
description: "使用 Sysdig 在 Azure 容器服务中监视 Kubernetes 群集"
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
ms.custom: mvc
ms.openlocfilehash: afe22b84015526f901111238e36baaa94694ccbf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>使用 Sysdig 监视 Azure 容器服务 Kubernetes 群集

## <a name="prerequisites"></a>先决条件
本演练假定用户已[使用 Azure 容器服务创建 Kubernetes 群集](container-service-kubernetes-walkthrough.md)，

并已安装 azure cli 和 kubectl 工具。

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

## <a name="sysdig"></a>Sysdig
Sysdig 以服务公司的身份进行外部监视，可监视在 Azure 中运行的 Kubernetes 群集的容器。 使用 Sysdig 需要有效的 Sysdig 帐户。
可在其[站点](https://app.sysdigcloud.com)上注册一个帐户。

登录到 Sysdig 云网站后，请单击用户名，在该页上你应看到自己的“访问密钥”。 

![Sysdig API 密钥](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>将 Sysdig 代理安装到 Kubernetes
为监视容器，Sysdig 会在使用 Kubernetes`DaemonSet` 的每台计算机上运行一个进程。
DaemonSets 是 Kubernetes API 对象，在每台计算机上运行一个容器实例。
非常适合安装 Sysdig 的监视代理等工具。

若要安装 Sysdig daemonset，首先应该从 sysdig 中下载[模板](https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml)。 将文件另存为 `sysdig-daemonset.yaml`。

在 Linux 和 OS X 上运行：

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

在 PowerShell 中运行：

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

然后编辑该文件，插入从 Sysdig 帐户获取的访问密钥。

最后创建 DaemonSet：

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>查看监视
代理安装完毕并开始运行后，应该将数据发送回 Sysdig。  返回到 [sysdig 仪表板](https://app.sysdigcloud.com)应该可以看到有关容器的信息。

还可以通过[新仪表板向导](https://app.sysdigcloud.com/#/dashboards/new)安 Kubernetes 特定的仪表板。

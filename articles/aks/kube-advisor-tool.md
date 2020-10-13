---
title: 检查部署的最佳做法
titleSuffix: Azure Kubernetes Service
description: 了解如何使用 kube-advisor 检查 Azure Kubernetes 服务上部署的最佳做法实现情况
services: container-service
author: seanmck
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 2b0078f1aff3ef81ee270f67de0fffddec3abab9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86255245"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>检查 Kubernetes 群集中的最佳做法

对于 Kubernetes 部署，有多个应遵循的最佳做法，以确保应用程序的最佳性能和复原能力。 kube-advisor 工具可用于查找不遵循这些建议的部署。

## <a name="about-kube-advisor"></a>关于 kube-advisor

[kube-advisor 工具][kube-advisor-github]是单个容器，为在群集上运行而设计。 它将查询 Kubernetes API 服务器，以了解你的部署，并返回一组建议的改进。

Kube-advisor 工具可以报告 Windows 应用程序和 Linux 应用程序的 PodSpecs 中缺少的资源请求和限制，但 kube-advisor 工具本身必须在 Linux pod 上计划。 可以使用 pod 配置中的[节点选择器][k8s-node-selector]，将 pod 计划为在具有特定 OS 的节点池上运行。

> [!NOTE]
> Microsoft 最大程度支持 kube-advisor 工具。 应在 GitHub 上提交的问题和建议。

## <a name="running-kube-advisor"></a>运行 kube-advisor

若要在针对[基于角色的访问控制 (RBAC)](./azure-ad-integration-cli.md) 配置的群集上运行此工具，请使用以下命令。 第一个命令可创建 Kubernetes 服务帐户。 第二个命令使用该服务帐户在 pod 中运行该工具，并配置 pod 以在退出后删除。 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }" --namespace default
```

如果不使用 RBAC，可以按如下所示运行命令：

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

在几秒内，应会看到介绍部署的潜在改进的表。

![kube-advisor 输出](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>执行的检查

该工具可验证多个 Kubernetes 最佳做法，每个都有其建议的修正。

### <a name="resource-requests-and-limits"></a>资源请求和限制

Kubernetes 支持定义 [pod 资源请求和限制规范][kube-cpumem]。 请求定义运行容器所需的最小 CPU 和内存。 限制定义应允许的最大 CPU 和内存。

默认情况下，未对 pod 规范设置任何请求或限制。 这可能导致节点被过度安排，而耗尽容器。 kube-advisor 工具可突出显示 pod，并且未设置请求和限制。

## <a name="cleaning-up"></a>清理

如果群集已启用 RBAC，可以使用以下命令在运行工具后清理 `ClusterRoleBinding`：

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

如果针对未启用 RBAC 的群集运行该工具，则不需要进行任何清理。

## <a name="next-steps"></a>后续步骤

- [排查 Azure Kubernetes 服务的问题](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors

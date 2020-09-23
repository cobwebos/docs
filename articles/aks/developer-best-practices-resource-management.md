---
title: 资源管理最佳做法
titleSuffix: Azure Kubernetes Service
description: 了解有关 Azure Kubernetes 服务 (AKS) 中的资源管理的应用程序开发人员最佳做法
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: e52bd150f72ba663c504b81832ce83d3e38cbf04
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90986779"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>有关管理 Azure Kubernetes 服务 (AKS) 中的资源的应用程序开发人员最佳做法

在 Azure Kubernetes 服务 (AKS) 中开发和运行应用程序时，需要考虑到几个重要方面。 如何应对应用程序部署可能对所提供服务的最终用户体验造成的负面影响。 为帮助获得成功，在 AKS 中开发和运行应用程序时，请遵循一些最佳做法。

本最佳做法文章从应用程序开发人员的角度重点介绍如何运行群集和工作负荷。 有关管理最佳做法的信息，请参阅[有关 Azure Kubernetes 服务 (AKS) 中隔离和资源管理的群集操作员最佳做法][operator-best-practices-isolation]。 本文内容：

> [!div class="checklist"]
> * pod 资源请求和限制是什么
> * 通过 Bridge 开发和部署应用程序并将其部署到 Kubernetes 和 Visual Studio Code 的方法
> * 如何使用 `kube-advisor` 工具检查部署问题

## <a name="define-pod-resource-requests-and-limits"></a>定义 pod 资源请求和限制

**最佳做法指导** - 在 YAML 清单中针对所有 pod 设置 pod 请求和限制。 如果 AKS 群集使用资源配额，而你未定义这些值，则可能会拒绝你的部署。

管理 AKS 群集中的计算资源的主要方法是使用 pod 请求和限制。 这些请求和限制可让 Kubernetes 计划程序知道应该为 pod 分配哪些计算资源。

* **Pod CPU/内存请求**定义 Pod 定期需要的固定 CPU 和内存量。
    * 当 Kubernetes 计划程序尝试在节点上放置 Pod 时，将使用 Pod 请求来确定哪个节点有足够的可用资源进行计划。
    * 如果未设置 Pod 请求，则默认情况下会将其设置为定义的限制。
    * 必须监视应用程序的性能并调整这些请求，这很重要。 如果发出的请求不足，应用程序可能会因节点计划过度而导致性能下降。 如果估算的请求数过高，则应用程序可能会更加难以进行计划。
* **Pod CPU/内存限制**是 Pod 可以使用的最大 CPU 和内存量。 内存限制有助于定义因资源不足而导致节点不稳定时应终止的 Pod。 如果没有适当的限制，则会终止固定的 Pod，直到解除资源压力。 Pod 在一段时间内不一定能够超过 CPU 限制，但是 Pod 不会因超过 CPU 限制而被终止。 
    * Pod 限制有助于定义 Pod 何时失去对资源消耗的控制。 超出限制时，会首先终止该 Pod 来维护节点运行状况，最大程度地减少对共享节点的 Pod 的影响。
    * 如果未设置 Pod 限制，则会将其默认设置为给定节点上的最高可用值。
    * 设置的 pod 限制不应超过节点可以支持的限制。 每个 AKS 节点将为核心 Kubernetes 组件保留一定的 CPU 和内存量。 应用程序可能会尝试消耗节点上的大量资源，使其他 pod 能够成功运行。
    * 同样，请在一天或一周的不同时间监视应用程序的性能，这很重要。 确定峰值需求在何时，并根据满足应用程序的最大需求所需的资源来调整 Pod 限制。

在 Pod 规范中，必须根据上述信息定义这些请求和限制，这是**最佳做法且很重要**。 如果不包含这些值，则 Kubernetes 计划程序无法考虑应用程序在制定决策时所需的资源。

如果计划程序在资源不足的节点上放置一个 Pod，则应用程序性能会下降。 强烈建议群集管理员针对需要你设置资源请求和限制的命名空间设置资源配额。 有关详细信息，请参阅 [AKS 群集上的资源配额][resource-quotas]。

定义 CPU 请求或限制时，值以 CPU 单位计量。 
* *1.0* CPU 相当于节点上的一个基础虚拟 CPU 核心。 
* GPU 使用与此相同的计量方法。
* 可以定义以毫核心数度量的分数。 例如，*100m* 表示 *0.1* 个基础 vCPU 核心。

在以下单个 NGINX pod 的基本示例中，pod 请求 *100m* 的 CPU 时间和 *128 Mi* 的内存。 pod 的资源限制设置为 *250m* CPU 和 *256Mi* 内存：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

有关资源度量和分配的详细信息，请参阅[管理容器的计算资源][k8s-resource-limits]。

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>针对 AKS 群集开发和调试应用程序

**最佳做法指南** -开发团队应使用 Bridge 到 Kubernetes 部署和调试 AKS 群集。

使用 Bridge 到 Kubernetes，可以直接针对 AKS 群集开发、调试和测试应用程序。 在整个应用程序生命周期，团队中的开发人员共同协作进行生成和测试。 可以继续使用现有的工具，例如 Visual Studio 或 Visual Studio Code。 为桥接 Kubernetes 安装了一个扩展，可让你直接在 AKS 群集中进行开发。

这一与 Kubernetes 的集成开发和测试过程可减少对本地测试环境（如 [minikube][minikube]）的需求。 可以针对 AKS 群集进行开发和测试。 可根据前面有关使用命名空间逻辑隔离群集的部分中所述保护和隔离此群集。

桥 to Kubernetes 适用于在 Linux pod 和节点上运行的应用程序。

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>使用适用于 Kubernetes 的 Visual Studio Code 扩展

**最佳做法指导** - 编写 YAML 清单时安装并使用适用于 Kubernetes 的 VS Code 扩展。 还可将该扩展用于集成式部署解决方案，不经常与 AKS 群集交互的应用程序所有者也许可从中获得帮助。

[适用于 Kubernetes 的 Visual Studio Code 扩展][vscode-kubernetes]可帮助你开发应用程序并将其部署到 AKS。 该扩展为 Kubernetes 资源以及 Helm 图表和模板提供 intellisense 功能。 还可以从 VS Code 内部浏览、部署和编辑 Kubernetes 资源。 该扩展还针对 pod 规范中设置的资源请求或限制提供 intellisense 检查：

![适用于 Kubernetes 的 VS Code 扩展中有关缺少内存限制的警告](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>定期使用 kube-advisor 检查应用程序问题

**最佳做法指导** - 定期运行最新版本的 `kube-advisor` 开放源代码工具，以检测群集中的问题。 如果针对现有 AKS 群集应用资源配额，请先运行 `kube-advisor`，以查找未定义资源请求和限制的 pod。

[kube-advisor][kube-advisor] 工具是一个关联的 AKS 开放源代码项目，它将扫描 Kubernetes 群集，并报告它找到的问题。 一项有用的检查是识别未应用资源请求和限制的 pod。

kube-advisor 工具可以报告 PodSpecs for Windows 应用程序以及 Linux 应用程序中缺少的资源请求和限制，但 kube-advisor 工具本身必须在 Linux Pod 上进行计划。 可以使用 Pod 配置中的[节点选择器][k8s-node-selector]安排 Pod 在具有特定 OS 的节点池上运行。

在托管许多开发团队和应用程序的 AKS 群集中，可能很难跟踪未设置这些资源请求和限制的 pod。 最佳做法是定期针对 AKS 群集运行 `kube-advisor`。

## <a name="next-steps"></a>后续步骤

本最佳做法文章从群集操作员的角度重点介绍了如何运行群集和工作负荷。 有关管理最佳做法的信息，请参阅[有关 Azure Kubernetes 服务 (AKS) 中隔离和资源管理的群集操作员最佳做法][operator-best-practices-isolation]。

若要实施其中的某些最佳做法，请参阅以下文章：

* [通过桥与 Kubernetes 进行开发][btk]
* [使用 kube-advisor 检查问题][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[btk]: /visualstudio/containers/overview-bridge-to-kubernetes
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors

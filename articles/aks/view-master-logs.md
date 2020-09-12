---
title: 查看 Azure Kubernetes 服务 (AKS) 控制器日志
description: 了解如何启用和查看 Azure Kubernetes 服务 (AKS) 中 Kubernetes 主节点的日志
services: container-service
ms.topic: article
ms.date: 01/03/2019
ms.openlocfilehash: a0207ebbb1596e41ad65e21a769d7041a239f767
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "90004861"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>启用和查看 Azure Kubernetes 服务 (AKS) 中 Kubernetes 主节点的日志

使用 Azure Kubernetes 服务 (AKS)，可以提供 *kube-apiserver* 和 *kube-controller-manager* 等主组件作为托管服务。 创建和管理运行 *kubelet* 与容器运行时的节点，并通过托管的 Kubernetes API 服务器部署应用程序。 为帮助排查应用程序和服务问题，可能需要查看这些主组件生成的日志。 本文介绍如何使用 Azure Monitor 日志从 Kubernetes 主组件启用和查询日志。

## <a name="before-you-begin"></a>准备阶段

本文要求在 Azure 帐户中运行一个现有的 AKS 群集。 如果还没有 AKS 群集，请使用 [Azure CLI][cli-quickstart] 或 [Azure 门户][portal-quickstart]创建一个。 Azure Monitor 日志适用于支持 RBAC 和不支持 RBAC 的 AKS 群集。

## <a name="enable-resource-logs"></a>启用资源日志

为帮助收集和审查来自多个源的数据，Azure Monitor 日志提供了查询语言和分析引擎，该引擎可提供环境的见解。 工作区用于整理和分析数据，并可与 Application Insights 和安全中心等其他 Azure 服务集成。 若要使用不同的平台分析日志，可以选择将资源日志发送到 Azure 存储帐户或事件中心。 有关详细信息，请参阅[什么是 Azure Monitor 日志？][log-analytics-overview]。

Azure Monitor 日志是在 Azure 门户中启用和管理的。 若要为 AKS 群集中的 Kubernetes 主组件启用日志收集，请在 Web 浏览器中打开 Azure 门户并完成以下步骤：

1. 选择 AKS 群集的资源组，例如 *myResourceGroup*。 不要选择包含单个 AKS 群集资源的资源组，例如 *MC_myResourceGroup_myAKSCluster_eastus*。
1. 在左侧选择“诊断设置”。
1. 选择 AKS 群集（如 *myAKSCluster*），然后选择 " **添加诊断设置**"。
1. 输入名称（例如 myAKSClusterLogs），然后选择“发送到 Log Analytics”选项。
1. 选择现有工作区或者创建新的工作区。 如果创建工作区，请提供工作区名称、资源组和位置。
1. 在可用日志列表中，选择要启用的日志。 在此示例中，启用 *kube-audit* 日志。 常见日志包括 kube-apiserver、kube-controller-manager 和 kube-scheduler。 启用 Log Analytics 工作区后，可以返回并更改收集的日志。
1. 准备就绪后，选择“保存”以启用收集选定日志。

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>在 AKS 群集上计划测试 pod

若要生成某些日志，请在 AKS 群集中创建新的 pod。 以下示例 YAML 清单可用于创建一个基本的 NGINX 实例。 在所选的编辑器中创建名为 `nginx.yaml` 的文件，并在其中粘贴以下内容：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
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
    ports:
    - containerPort: 80
```

使用 [kubectl create][kubectl-create] 命令创建 Pod 并指定 YAML 文件，如以下示例所示：

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>查看收集的日志

启用并显示诊断日志可能需要几分钟时间。 在 Azure 门户中，导航到 AKS 群集，并选择左侧的 " **日志** "。 如果出现 " *示例查询* " 窗口，则将其关闭。

在左侧选择“日志”。 若要查看 *kube-audit* 日志，请在文本框中输入以下查询：

```
KubePodInventory
| where TimeGenerated > ago(1d)
```

许多日志可能会返回。 若要将查询范围缩小到查看有关上一步骤中创建的 NGINX pod 的日志，请添加其他 *where* 语句来搜索 *NGINX* ，如下面的示例查询中所示：

```
KubePodInventory
| where TimeGenerated > ago(1d)
| where Name contains "nginx"
```

有关如何查询和筛选日志数据的详细信息，请参阅[查看或分析使用 Log Analytics 日志搜索收集的数据][analyze-log-analytics]。

## <a name="log-event-schema"></a>日志事件架构

AKS 记录以下事件：

* [AzureActivity][log-schema-azureactivity]
* [AzureMetrics][log-schema-azuremetrics]
* [ContainerImageInventory][log-schema-containerimageinventory]
* [ContainerInventory][log-schema-containerinventory]
* [ContainerLog][log-schema-containerlog]
* [ContainerNodeInventory][log-schema-containernodeinventory]
* [ContainerServiceLog][log-schema-containerservicelog]
* [检测信号][log-schema-heartbeat]
* [InsightsMetrics][log-schema-insightsmetrics]
* [KubeEvents][log-schema-kubeevents]
* [KubeHealth][log-schema-kubehealth]
* [KubeMonAgentEvents][log-schema-kubemonagentevents]
* [KubeNodeInventory][log-schema-kubenodeinventory]
* [KubePodInventory][log-schema-kubepodinventory]
* [KubeServices][log-schema-kubeservices]
* [性能][log-schema-perf]

## <a name="log-roles"></a>日志角色

| 角色                     | 说明 |
|--------------------------|-------------|
| *aksService*             | 审核日志中控制平面操作的显示名称（来自 hcpService） |
| *masterclient*           | 审核日志中 MasterClientCertificate（通过 az aks get-credentials 获得的证书）的显示名称 |
| *nodeclient*             | 代理节点使用的 ClientCertificate 的显示名称 |

## <a name="next-steps"></a>后续步骤

本文已介绍如何启用和查看 AKS 群集中 Kubernetes 主组件的日志。 若要进一步进行监视和故障排除，还可以[查看 Kubelet 日志][kubelet-logs]并[启用 SSH 节点访问][aks-ssh]。

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/log-query/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/log-query/get-started-portal.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[log-schema-azureactivity]: /azure/azure-monitor/reference/tables/azureactivity
[log-schema-azuremetrics]: /azure/azure-monitor/reference/tables/azuremetrics
[log-schema-containerimageinventory]: /azure/azure-monitor/reference/tables/containerimageinventory
[log-schema-containerinventory]: /azure/azure-monitor/reference/tables/containerinventory
[log-schema-containerlog]: /azure/azure-monitor/reference/tables/containerlog
[log-schema-containernodeinventory]: /azure/azure-monitor/reference/tables/containernodeinventory
[log-schema-containerservicelog]: /azure/azure-monitor/reference/tables/containerservicelog
[log-schema-heartbeat]: /azure/azure-monitor/reference/tables/heartbeat
[log-schema-insightsmetrics]: /azure/azure-monitor/reference/tables/insightsmetrics
[log-schema-kubeevents]: /azure/azure-monitor/reference/tables/kubeevents
[log-schema-kubehealth]: /azure/azure-monitor/reference/tables/kubehealth
[log-schema-kubemonagentevents]: /azure/azure-monitor/reference/tables/kubemonagentevents
[log-schema-kubenodeinventory]: /azure/azure-monitor/reference/tables/kubenodeinventory
[log-schema-kubepodinventory]: /azure/azure-monitor/reference/tables/kubepodinventory
[log-schema-kubeservices]: /azure/azure-monitor/reference/tables/kubeservices
[log-schema-perf]: /azure/azure-monitor/reference/tables/perf

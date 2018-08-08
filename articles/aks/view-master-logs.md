---
title: 查看 Azure Kubernetes 服务 (AKS) 控制器日志
description: 了解如何启用和查看 Azure Kubernetes 服务 (AKS) 中 Kubernetes 主节点的日志
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 07/26/2018
ms.author: iainfou
ms.openlocfilehash: 05bfa4a65098245c43b97af296bf6f26950d018f
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330619"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>启用和查看 Azure Kubernetes 服务 (AKS) 中 Kubernetes 主节点的日志

使用 Azure Kubernetes 服务 (AKS)，可以提供 *kube-apiserver* 和 *kube-controller-manager* 等主组件作为托管服务。 创建和管理运行 *kubelet* 与容器运行时的节点，并通过托管的 Kubernetes API 服务器部署应用程序。 为帮助排查应用程序和服务问题，可能需要查看这些主组件生成的日志。 本文介绍如何使用 Azure Log Analytics 从 Kubernetes 主组件启用和查询日志。

## <a name="before-you-begin"></a>开始之前

本文要求在 Azure 帐户中运行一个现有的 AKS 群集。 如果没有 AKS 群集，请使用 [Azure CLI][cli-quickstart] 或 [Azure 门户][portal-quickstart]创建一个群集。 Log Analytics 适用于支持 RBAC 和不支持 RBAC 的 AKS 群集。

## <a name="enable-diagnostics-logs"></a>启用诊断日志

为帮助收集和审查来自多个源的数据，Log Analytics 提供了查询语言和分析引擎，该引擎可提供环境的见解。 工作区用于整理和分析数据，并可与 Application Insights 和安全中心等其他 Azure 服务集成。 若要使用不同的平台分析日志，可以选择将诊断日志发送到 Azure 存储帐户或事件中心。 有关详细信息，请参阅[什么是 Azure Log Analytics？][log-analytics-overview]。

在 Azure 门户中启用和管理 Log Analytics。 若要为 AKS 群集中的 Kubernetes 主组件启用日志收集，请在 Web 浏览器中打开 Azure 门户并完成以下步骤：

1. 选择 AKS 群集的资源组，例如 *myResourceGroup*。 不要选择包含单个 AKS 群集资源的资源组，例如 *MC_myResourceGroup_myAKSCluster_eastus*。
1. 在左侧选择“诊断设置”。
1. 选择 AKS 群集（例如 *myAKSCluster*），然后选择“启用诊断”。
1. 输入名称（例如 *myAKSLogs*），然后选择“发送到 Log Analytics”选项。
    * 选择*配置* Log Analytics，然后选择现有的工作区，或**创建新工作区**。
    * 如果需要创建工作区，请提供一个名称、资源组和位置。
1. 在可用日志列表中，选择要启用的日志，例如 *kube-apiserver*、*kube-controller-manager* 和 *kube-scheduler*。 启用 Log Analytics 后，可以返回并更改收集的日志。
1. 准备就绪后，选择“保存”以启用选定日志的收集。

以下示例门户屏幕截图显示了“诊断设置”窗口，以及用于创建 OMS 工作区的选项：

![为 AKS 群集的 Log Analytics 启用 OMS 工作区](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>在 AKS 群集上计划测试 pod

若要生成某些日志，请在 AKS 群集中创建新的 pod。 以下示例 YAML 清单可用于创建一个基本的 NGINX 实例。 在所选的编辑器中创建名为 `nginx.yaml` 的文件，并在其中粘贴以下内容：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: myfrontend
    image: nginx
    ports:
    - containerPort: 80
```

使用 [kubectl create][kubectl-create] 命令创建 Pod 并指定 YAML 文件，如以下示例所示：

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>查看收集的日志

可能需要等待几分钟，诊断日志才会启用并显示在 OMS 工作区中。 在 Azure 门户中，选择 Log Analytics 工作区的资源组（例如 *myResourceGroup*），然后选择 Log Analytics 资源（例如 *myAKSLogs*）。

![选择 AKS 群集的 Log Analytics 工作区](media/view-master-logs/select-log-analytics-workspace.png)

在左侧选择“日志搜索”。 若要查看 *kube-apiserver*，请在文本框中输入以下查询：

```
search *
| where Type == "AzureDiagnostics"
| where Category == "kube-apiserver"
| project log_s
```

可能返回了 API 服务器的多个日志。 若要缩小查询范围，以便查看上一步骤中创建的 NGINX pod 的相关日志，请额外添加一个 *where* 语句来搜索 *pod/nginx*，如以下示例查询中所示：

```
search *
| where Type == "AzureDiagnostics"
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

此时会显示 NGINX pod 的特定日志，如以下示例屏幕截图中所示：

![示例 NGINX pod 的 Log Analytics 查询结果](media/view-master-logs/log-analytics-query-results.png)

若要查看其他日志，可将针对 *Category* 名称的查询更新为 *kube-controller-manager* 或 *kube-scheduler*，具体取决于启用的其他日志。 然后，可以使用附加的 *where* 语句来具体化要查找的事件。

有关如何查询和筛选日志数据的详细信息，请参阅[查看或分析使用 Log Analytics 日志搜索收集的数据][analyze-log-analytics]。

## <a name="log-event-schema"></a>日志事件架构

为帮助分析日志数据，下表详细说明了用于每个事件的架构：

| 字段名称               | Description |
|--------------------------|-------------|
| *resourceId*             | 生成日志的 Azure 资源 |
| *time*                   | 上传日志的时间戳 |
| *category*               | 生成日志的容器/组件的名称 |
| *operationName*          | 始终为 *Microsoft.ContainerService/managedClusters/diagnositicLogs/Read* |
| *properties.log*         | 来自组件的日志的完整文本 |
| *properties.stream*      | *stderr* 或 *stdout* |
| *properties.pod*         | 日志的来源 pod 名称 |
| *properties.containerID* | 此日志的来源 Docker 容器的 ID |

## <a name="next-steps"></a>后续步骤

本文已介绍如何启用和查看 AKS 群集中 Kubernetes 主组件的日志。 若要进一步进行监视和排除故障，还可以[查看 Kubelet 日志][kubelet-logs]并[启用 SSH 节点访问][aks-ssh]。

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../log-analytics/log-analytics-tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: aks-ssh.md
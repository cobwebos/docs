---
title: Azure Monitor 为容器部署 & HPA 指标 |Microsoft Docs
description: 本文介绍) 为容器 (Azure Monitor 收集指标的部署 & HPA）。
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: ee1f0d4849a8382a898aaca84956ff78166e138f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89570442"
---
# <a name="deployment--hpa-metrics-with-azure-monitor-for-containers"></a>部署 & HPA 指标与容器 Azure Monitor

从代理版本 *ciprod08072020*开始，适用于容器的 Azure monitor 集成代理现在为部署 & hpa 收集指标。

## <a name="deployment-metrics"></a>部署指标

容器 Azure Monitor 自动开始监视部署，方法是收集以下指标60秒的时间间隔，并将其存储在 **InsightMetrics** 表中：

|指标名称 |指标维度（标记） |说明 |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId、container.azm.ms/clusterName、creationTime、deployment、deploymentStrategy、k8sNamespace、spec_replicas、status_replicas_available status_replicas_updated (状态. updatedReplicas)  | 此部署所面向的就绪盒总数 (readyReplicas) 。 下面是此指标的维度。 <ul> <li> 部署-部署的名称 </li> <li> k8sNamespace-部署的 Kubernetes 命名空间 </li> <li> deploymentStrategy-用于将 pod 替换为新 (规范的部署策略) </li><li> creationTime-部署创建时间戳 </li> <li> spec_replicas-所需的 pod (规范)  </li> <li>status_replicas_available-此部署 (目标) 目标 (的可用箱总数至少为 minReadySeconds) </li><li>status_replicas_updated-此部署的目标为具有所需的模板规范的非终止箱的总数 (updatedReplicas)  </li></ul>|

## <a name="hpa-metrics"></a>HPA 指标

容器 Azure Monitor 自动开始监视 Hpa，方法是：按60秒间隔收集以下指标，并将其存储在 **InsightMetrics** 表中：

|指标名称 |指标维度（标记） |说明 |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId、container.azm.ms/clusterName、creationTime、hpa、k8sNamespace、lastScaleTime、spec_max_replicas、spec_min_replicas、status_desired_replicas、targetKind、targetName | 此自动缩放程序管理的 pod 副本的当前数量 (currentReplicas) 。 下面是此指标的维度。 <ul> <li> hpa-HPA 的名称 </li> <li> HPA 的 k8sNamespace Kubernetes 命名空间 </li> <li> lastScaleTime-上次 HPA 将 pod 数量放大 (状态。 lastScaleTime) </li><li> creationTime-HPA 创建时间戳 </li> <li> spec_max_replicas-可由自动缩放程序 (maxReplicas 的 pod 数的上限)  </li> <li> spec_min_replicas-自动缩放程序可缩减的副本数的下限 (minReplicas)  </li><li>此自动缩放程序管理的 status_desired_replicas 所需的 pod 副本数 (desiredReplicas) </li><li>targetKind-HPA 的目标 (类型： scaleTargetRef)  </li><li>targetName-HPA 的目标 (spec.scaleTargetRef.name 的名称)  </li></ul>|

## <a name="deployment--hpa-charts"></a>部署 & HPA 图 

容器 Azure Monitor 包括预配置的图表，适用于表中前面列出的指标，作为每个分类的工作簿。 你可以通过从左侧窗格中选择工作簿，并从见解中的 "**查看工作簿**" 下拉列表中选择 "**工作**簿"，来直接从 AKS 群集中找到部署 & HPA 工作簿**部署 & HPA** 。

## <a name="next-steps"></a>后续步骤

- 查看 [Kubernetes 中的 Kube 指标](https://github.com/kubernetes/kube-state-metrics/tree/master/docs) ，了解有关 Kube 状态指标的详细信息。
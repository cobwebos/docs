---
title: Azure SQL Edge 容器的高可用性-Azure SQL Edge
description: 了解 Azure SQL Edge 容器的高可用性
keywords: SQL 边缘，容器，高可用性
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56b9f06547f737bc05d573f98ce1dbac2ba48758
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933441"
---
# <a name="high-availability-for-azure-sql-edge-containers"></a>Azure SQL Edge 容器的高可用性

在 Kubernetes 中以本机方式创建和管理 Azure SQL Edge 实例。 将 Azure SQL Edge 部署到由 [Kubernetes](https://kubernetes.io/)管理的 docker 容器。 在 Kubernetes 中，具有 Azure SQL Edge 实例的容器可以在群集节点出现故障时自动恢复。 可以使用 Kubernetes 永久性卷声明 (PVC) 来配置 SQL Edge 容器映像。 Kubernetes 监视容器中的 Azure SQL Edge 进程。 如果进程、Pod、容器或节点发生故障，Kubernetes 将自动启动另一个实例，并重新连接到存储。

## <a name="azure-sql-edge-containers-on-kubernetes"></a>Kubernetes 上的 Azure SQL Edge 容器

Kubernetes 1.6 和更高版本支持 [*存储类*](https://kubernetes.io/docs/concepts/storage/storage-classes/)、 [*永久性卷声明*](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims)。

在此配置中，Kubernetes 扮演容器业务流程协调程序一角。 

![Kubernetes 群集中的 Azure SQL Edge 示意图](media/deploy-kubernetes/kubernetes-sql-edge.png)

在上图中，`azure-sql-edge`是 [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/) 中的容器。 Kubernetes 协调群集中的资源。 [副本集](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)可确保在节点发生故障后自动恢复 Pod。 应用程序会连接到服务。 在这种情况下，该服务表示负载均衡器，承载着 `azure-sql-edge` 发生故障后保持不变的 IP 地址。

在下图中，`azure-sql-edge` 容器发生了故障。 作为业务流程协调程序，Kubernetes 可保证副本集中正常实例的计数正确，并根据配置启动新容器。 业务流程协调程序会在同一节点上启动新 Pod，并且 `azure-sql-edge` 会重新连接到同一个永久性存储。 该服务会连接到重新创建的 `azure-sql-edge`。

![Pod 出现故障后 Kubernetes 群集中的 Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

在下图中，托管 `azure-sql-edge` 容器的节点发生了故障。 业务流程协调程序在不同的节点上启动新 Pod，并且 `azure-sql-edge` 重新连接到同一个永久性存储。 该服务会连接到重新创建的 `azure-sql-edge`。

![节点出现故障后 Kubernetes 群集中的 Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png).

若要在 Kubernetes 中创建容器，请参阅 [在 Kubernetes 中部署 AZURE SQL Edge 容器](deploy-Kubernetes.md)

## <a name="next-steps"></a>后续步骤

若要在 Azure Kubernetes 服务中部署 Azure SQL Edge 容器 (AKS) ，请参阅以下文章：
- [在 Kubernetes 中部署 Azure SQL Edge 容器](deploy-Kubernetes.md)
- [在 SQL Edge 中将机器学习和人工智能与 ONNX 结合使用](onnx-overview.md)。
- [使用 IoT Edge 通过 SQL Edge 生成端到端 IoT 解决方案](tutorial-deploy-azure-resources.md)。
- [Azure SQL Edge 中的数据流式处理](stream-data.md)
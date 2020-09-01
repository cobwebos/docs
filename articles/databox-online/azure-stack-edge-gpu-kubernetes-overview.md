---
title: Microsoft Azure Stack 边缘设备上的 Kubernetes 群集概述 |Microsoft Docs
description: 描述如何在 Azure Stack 边缘设备上实现 Kubernetes。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: b85586a431a20102035e253537fc45c8a8a54796
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083147"
---
# <a name="kubernetes-on-your-azure-stack-edge-device"></a>Azure Stack 边缘设备上的 Kubernetes

Kubernetes 是一种常用的开源平台，用于协调容器化应用程序。 本文概述了 Kubernetes，并介绍了 Kubernetes 在 Azure Stack 边缘设备上的工作方式。 

## <a name="about-kubernetes"></a>关于 Kubernetes 

Kubernetes 提供了一个简单可靠的平台，用于管理基于容器的应用程序及其关联的网络和存储组件。 可以通过 Kubernetes 快速生成、交付和缩放容器化应用。

作为开放平台，你可以使用 Kubernetes，通过你喜欢的编程语言、操作系统库或消息传递总线来构建应用程序。 若要计划和部署发布，Kubernetes 可与现有持续集成和持续交付工具集成。

有关详细信息，请参阅 [Kubernetes 的工作](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s)原理。

## <a name="kubernetes-on-azure-stack-edge"></a>Azure Stack Edge 上的 Kubernetes

在 Azure Stack 边缘设备上，可以通过配置计算来创建 Kubernetes 群集。 配置计算角色后，将为你部署和配置 Kubernetes 群集，包括主节点和辅助节点。 然后 `kubectl` ，通过、IoT Edge 或 Azure Arc 使用此群集进行工作负荷部署。

Azure Stack 边缘设备作为构成基础结构群集的1节点配置提供。 Kubernetes 群集与基础结构群集分开，并部署在基础结构群集之上。 基础结构群集为 Azure Stack 边缘设备提供持久存储，而 Kubernetes 群集仅负责应用程序业务流程。 

在这种情况下，Kubernetes 群集有一个主节点和一个辅助角色节点。 群集中的 Kubernetes 节点是运行你的应用程序和云工作流的虚拟机。 

Kubernetes 主节点负责维护群集所需的状态。 主节点还控制辅助节点，后者又运行容器化应用程序。 

下图说明了 Kubernetes 在1节点 Azure Stack 边缘设备上的实现。 1节点设备不具备高可用性，如果单个节点出现故障，设备将会关闭。 Kubernetes 群集也会关闭。

![1节点 Azure Stack Edge 设备的 Kubernetes 体系结构](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

有关 Kubernetes 群集体系结构的详细信息，请参阅 [Kubernetes 核心概念](https://kubernetes.io/docs/concepts/architecture/)。


<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>存储卷预配

若要支持应用程序工作负载，可以在 Azure Stack 边缘设备共享上装载存储卷以存放永久性数据。 静态和动态卷都可以使用。 

有关详细信息，请参阅 [Azure Stack Edge 设备的 Kubernetes 存储](azure-stack-edge-gpu-kubernetes-storage.md)中的应用程序的存储预配选项。

## <a name="networking"></a>网络

Kubernetes 网络允许配置 Kubernetes 网络内的通信，包括容器到容器网络、pod 到 pod 网络、pod 到服务网络以及 Internet 到服务网络。 有关详细信息，请参阅 [Azure Stack Edge 设备的 Kubernetes 网络](azure-stack-edge-gpu-kubernetes-networking.md)中的网络模型。

## <a name="updates"></a>更新

当新 Kubernetes 版本可用时，可以使用可用于 Azure Stack Edge 设备的标准更新来升级群集。 有关如何升级的步骤，请参阅为 [Azure Stack 边缘应用更新](azure-stack-edge-gpu-install-update.md)。

## <a name="access-monitoring"></a>访问，监视

Azure Stack 边缘设备上的 Kubernetes 群集允许 (RBAC) 基于角色的访问控制。 有关详细信息，请参阅 [Azure Stack Edge 设备上的 Kubernetes 群集的基于角色的访问控制](azure-stack-edge-gpu-kubernetes-rbac.md)。

还可以通过 Kubernetes 仪表板监视群集和资源的运行状况。 容器日志也可用。 有关详细信息，请参阅 [使用 Kubernetes 仪表板监视 Azure Stack 边缘设备上的 Kubernetes 群集运行状况](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)。

Azure Monitor 也可作为加载项提供，用于从容器、节点和控制器收集运行状况数据。 有关详细信息，请参阅 [Azure Monitor 概述](../azure-monitor/overview.md)

<!--## Private container registry

Kubernetes on Azure Stack Edge device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>应用程序管理

在 Azure Stack 边缘设备上创建 Kubernetes 群集后，可以通过以下任一方法管理在此群集上部署的应用程序：

- 本机访问方式 `kubectl`
- IoT Edge 
- Azure Arc

以下部分介绍了这些方法。


### <a name="kubernetes-and-kubectl"></a>Kubernetes 和 kubectl

部署 Kubernetes 群集后，你可以从客户端计算机本地管理群集上部署的应用程序。 通过命令行使用本机工具（如 *kubectl* ）来与应用程序进行交互。 

有关部署 Kubernetes 群集的详细信息，请参阅 [在 Azure Stack Edge 设备上部署 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)。 有关管理的信息，请参阅 [使用 kubectl 管理 Azure Stack 边缘设备上的 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)。


### <a name="kubernetes-and-iot-edge"></a>Kubernetes 和 IoT Edge

Kubernetes 还可以与 Azure Stack 边缘设备上的 IoT Edge 工作负荷集成，在 Kubernetes 提供规模，而生态系统和 IoT 提供 IoT 中心的生态系统。 Kubernetes 层用作部署 Azure IoT Edge 工作负荷的基础结构层。 模块生存期和网络负载平衡由 Kubernetes 管理，而 edge 应用程序平台由 IoT Edge 管理。

有关通过 IoT Edge 在 Kubernetes 群集上部署应用程序的详细信息，请参阅： 

- [通过 IoT Edge 在 Azure Stack Edge 设备上公开无状态应用程序](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)。


### <a name="kubernetes-and-azure-arc"></a>Kubernetes 和 Azure Arc

Azure Arc 是一种混合管理工具，可用于在 Kubernetes 群集上部署应用程序。 使用 Azure Arc，还可以使用容器 Azure Monitor 来查看和监视群集。 有关详细信息，请参阅 [什么是启用了 Azure Arc 的 Kubernetes？](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)。 有关 Azure Arc 定价的信息，请参阅 [Azure arc 定价](https://azure.microsoft.com/services/azure-arc/#pricing)。


## <a name="next-steps"></a>后续步骤

- 在 [Azure Stack Edge 设备](azure-stack-edge-gpu-kubernetes-storage.md)上了解有关 Kubernetes 存储的详细信息。
- 了解 [Azure Stack 边缘设备](azure-stack-edge-gpu-kubernetes-networking.md)上的 Kubernetes 网络模型。
- 将 [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) 部署 Azure 门户。

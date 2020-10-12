---
title: 了解 Azure Stack Edge Pro 设备上的 Kubernetes 网络 |Microsoft Docs
description: 介绍 Kubernetes 网络如何在 Azure Stack Edge Pro 设备上工作。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: alkohli
ms.openlocfilehash: 001304ad6eda27db2285aaa9ad8b28929e2a04f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899317"
---
# <a name="kubernetes-networking-in-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 设备中的 Kubernetes 网络

在 Azure Stack Edge Pro 设备上，配置计算角色时，会创建 Kubernetes 群集。 创建 Kubernetes 群集后，可以在 pod 中的 Kubernetes 群集上部署容器化应用程序。 在 Kubernetes 群集中，可以通过不同的方式来使用网络箱。 

本文大致介绍了 Kubernetes 群集中的网络，特别是在 Azure Stack Edge Pro 设备的上下文中。 

## <a name="networking-requirements"></a>网络要求

下面是部署到 Kubernetes 群集的典型2层应用的示例。

- 该应用程序的后端中有一个 web 服务器前端和一个数据库应用程序。 
- 为每个 pod 分配一个 IP，但这些 ip 在 pod 的重新启动和故障转移时可能会发生更改。 
- 每个应用程序都由多个 pod 组成，每个 pod 副本应有流量的负载均衡。 

![Kubernetes 网络要求](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1.png)

上述方案导致以下网络要求：

 - Kubernetes 群集外部的应用程序用户需要通过名称或 IP 地址访问面向外部的应用程序。 
 - Kubernetes 群集中的应用程序（例如，前端和后端 pod）应该能够彼此通信。

若要解决上述需求，我们介绍了 Kubernetes 服务。 


## <a name="networking-services"></a>网络服务

Kubernetes 服务有两种类型： 

- **群集 IP 服务** -将此视为提供应用程序盒的常量终结点。 无法从 Kubernetes 群集外部访问与这些服务关联的任何 pod。 用于这些服务的 IP 地址来自专用网络中的地址空间。 
- **负载均衡器 ip** 类似于群集 ip 服务，但关联的 ip 来自外部网络，可从 Kubernetes 群集外部进行访问。


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->


## <a name="kubernetes-networking-on-azure-stack-edge-pro"></a>Azure Stack Edge Pro 上的 Kubernetes 网络

"Calico"、"Metallb" 和 "核心" DNS 是安装在 Azure Stack Edge Pro 上的所有组件。 

- **Calico** 将专用 ip 范围中的 ip 地址分配给每个 pod，并为这些 pod 配置网络，以便一个节点上的 pod 可以与另一个节点上的 pod 通信。 
- **Metallb** 在群集中的 pod 上运行，并将 IP 地址分配给类型为负载均衡器的服务。 负载均衡器 IP 地址是从通过本地 UI 提供的服务 Ip 范围中选择的。 
- **核心 DNS** -此加载项将 DNS 记录映射服务名称配置为群集 IP 地址。

用于 Kubernetes 节点和外部服务的 IP 地址是通过设备本地 UI 中的 " **计算网络** " 页面提供的。

![本地 UI 中的 Kubernetes IP 分配](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

IP 分配适用于：

- **Kubernetes 节点 ip**：此 IP 范围用于 Kubernetes 主节点和辅助角色节点。 Kubernetes 节点相互通信时，将使用这些 IP。

- **Kubernetes 外部服务 ip**：此 IP 范围用于外部服务 (也称为) 在 Kubernetes 群集外公开的负载平衡器服务。  


## <a name="next-steps"></a>后续步骤

若要在 Azure Stack Edge Pro 上配置 Kubernetes 网络，请参阅：

- [通过 IoT Edge 在 Azure Stack Edge Pro 的外部公开无状态应用程序](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)。

- [通过 kuebctl 在 Azure Stack Edge Pro 上从外部公开无状态应用程序](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)。

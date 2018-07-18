---
title: 将 Azure Kubernetes 服务 (AKS) 群集的出口流量列入允许列表
description: 将 Azure Kubernetes 服务 (AKS) 群集的出口流量列入允许列表
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/23/2018
ms.author: iainfou
ms.openlocfilehash: 2394b61fb84a2f22af036f35819b87074a1dbd2d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100139"
---
# <a name="azure-kubernetes-service-aks-egress"></a>Azure Kubernetes 服务 (AKS) 出口

默认情况下，Azure Kubernetes 服务 (AKS) 群集的出口地址是随机分配的。 当需要标识用于访问外部服务的 IP 地址时，此配置不理想。 本文档详细介绍了如何创建和维护 AKS 群集中的静态分配的出口 IP 地址。

## <a name="egress-overview"></a>出口概述

AKS 群集的出口流量遵循 Azure 负载均衡器约定，[此处][outbound-connections]记录了这些约定。 在创建 `LoadBalancer` 类型的第一个 Kubernetes 服务之前，代理节点不是任何 Azure 负载均衡器池的一部分。 在此配置中，节点没有实例级公用 IP 地址。 Azure 将出站流转换为不可配置的或确定性的公用源 IP 地址。

创建 `LoadBalancer` 类型的 Kubernetes 服务后，会向 Azure 负载均衡器池添加代理节点。 对于出站流，Azure 将其转换为在负载均衡器上配置的第一个公用 IP 地址。

## <a name="create-a-static-public-ip"></a>创建静态公共 IP

为防止使用随机 IP 地址，请创建一个静态 IP 地址并确保负载均衡器使用此地址。 需要在 AKS“节点”资源组中创建 IP 地址。

使用 [az resource show][az-resource-show] 命令获取资源组名称。 更新资源组名称和群集名称，使之与环境相匹配。

```
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

接下来，使用 [az network public-ip create][public-ip-create] 命令创建静态公用 IP 地址。 更新资源组名称以匹配在最后一步中收集的名称。

```console
$ az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o table

Result
-------------
23.101.128.81
```

## <a name="create-a-service-with-the-static-ip"></a>使用静态 IP 创建服务

现在，你已有一个 IP 地址，请创建一个 `LoadBalancer` 类型的 Kubernetes 服务并将该 IP 地址分配给该服务。

创建名为 `egress-service.yaml` 的文件，并将其复制到以下 YAML 中。 更新 IP 地址以匹配你的环境

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-egress
spec:
  loadBalancerIP: 23.101.128.81
  type: LoadBalancer
  ports:
  - port: 8080
```

使用 `kubectl apply` 命令创建服务和部署。

```console
$ kubectl apply -f egress-service.yaml

service "aks-egress" created
```

创建此服务将在 Azure 负载均衡器上配置一个新的前端 IP。 如果没有配置任何其他 IP，则**所有**出口流量现在都应当使用此地址。 当在 Azure 负载均衡器上配置了多个地址时，出口将使用该负载均衡器上的第一个 IP。

## <a name="verify-egress-address"></a>验证出口地址

若要验证是否正在使用该公用 IP 地址，请使用诸如 `checkip.dyndns.org` 的服务。

启动并附加到 pod：

```console
$ kubectl run -it --rm aks-ip --image=debian
```

如果需要，在容器中安装 curl：

```console
$ apt-get update && apt-get install curl -y
```

对 `checkip.dyndns.org` 进行 Curl，这将返回出口 IP 地址：

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

你应当会看到该 IP 地址与附加到 Azure 负载均衡器的静态 IP 地址匹配。

## <a name="ingress-controller"></a>入口控制器

若要避免在 Azure 负载均衡器上维护多个公用 IP 地址，请考虑使用入口控制器。 入口控制器提供的好处包括：负载均衡、SSL/TLS 终止、对 URI 重写的支持以及上游 SSL/TLS 加密。 有关 AKS 中的入口控制器的详细信息，请参阅[在 AKS 群集中配置 NGINX 入口控制器][ingress-aks-cluster]指南。

## <a name="next-steps"></a>后续步骤

详细了解本文档中演示的软件。

- [Helm CLI][helm-cli-install]
- [NGINX 入口控制器][nginx-ingress]
- [Azure 负载均衡器出站连接][outbound-connections]

<!-- LINKS - internal -->
[az-resource-show]: /cli/azure/resource#az-resource-show
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[create-aks-cluster]: ./kubernetes-walkthrough.md
[helm-cli-install]: ./kubernetes-helm.md#install-helm-cli
[ingress-aks-cluster]: ./ingress.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create

<!-- LINKS - external -->
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

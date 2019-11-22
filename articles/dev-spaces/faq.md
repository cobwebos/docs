---
title: 常见问题 Azure Dev Spaces
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: 查找有关 Azure Dev Spaces 的一些常见问题的解答
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
ms.openlocfilehash: 3c7335f1656d304d231c2146c8b7496ea43f0b4c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280241"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>常见问题 Azure Dev Spaces

这解决了有关 Azure Dev Spaces 的常见问题。

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>哪些 Azure 区域当前提供 Azure Dev Spaces？

有关可用区域的完整列表，请参阅[支持的区域和配置][supported-regions]。

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>是否可以使用没有公共 IP 地址的 Azure Dev Spaces？

不能，无法在没有公共 IP 的 AKS 群集上预配 Azure Dev Spaces。 [Azure Dev Spaces 路由需要][dev-spaces-routing]公共 IP。

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>能否将我自己的入口用于 Azure Dev Spaces？

是的，你可以配置自己的入口，Azure Dev Spaces 创建一个。 例如，可以使用[traefik][ingress-traefik]。

## <a name="can-i-use-https-with-azure-dev-spaces"></a>是否可以将 HTTPS 与 Azure Dev Spaces 一起使用？

是的，你可以使用[traefik][ingress-https-traefik]配置你自己的入口和 HTTPS。

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>能否在使用 CNI 而不是 kubenet 的群集上使用 Azure Dev Spaces？ 

是的，可以在使用 CNI 进行网络连接的 AKS 群集上使用 Azure Dev Spaces。 例如，可以在具有[现有 Windows 容器][windows-containers]的 AKS 群集上使用 Azure Dev Spaces，使用 CNI 进行网络处理。

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>是否可以将 Azure Dev Spaces 与 Windows 容器一起使用？

目前，Azure Dev Spaces 仅在 Linux pod 和节点上运行，但你可以使用[现有的 Windows 容器][windows-containers]在 AKS 群集上运行 Azure Dev Spaces。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>能否在启用 API 服务器授权 IP 地址范围的 AKS 群集上使用 Azure Dev Spaces？

是的，你可以使用启用了[API 服务器授权 IP 地址范围][aks-auth-range]的 AKS 群集上的 Azure Dev Spaces。 [创建][aks-auth-range-create]群集时，必须[根据区域允许其他范围][aks-auth-range-ranges]。 还可以[更新][aks-auth-range-update]现有群集以允许其他范围。

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>能否在群集节点的受限制传出流量的 AKS 群集上使用 Azure Dev Spaces？

是的，在允许使用以下 Fqdn 后，可以在启用[群集节点的受限制传出流量][aks-restrict-egress-traffic]的 AKS 群集上使用 Azure Dev Spaces：

| FQDN                                    | 端口      | 使用      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | 请求 linux alpine 和其他 Azure Dev Spaces 映像 |
| gcr.io | HTTP：443 | 请求 helm/tiller 映像|
| storage.googleapis.com | HTTP：443 | 请求 helm/tiller 映像|


[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md
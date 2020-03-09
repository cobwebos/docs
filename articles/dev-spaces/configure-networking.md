---
title: 为不同网络拓扑中的 Azure Dev Spaces 配置网络
services: azure-dev-spaces
ms.date: 01/10/2020
ms.topic: conceptual
description: 描述在 Azure Kubernetes 服务中运行 Azure Dev Spaces 的网络要求
keywords: Azure Dev Spaces，Dev 空间，Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器，CNI，kubenet，SDN，网络
ms.openlocfilehash: 9e32e3b65451dceefaeeaf7faed7c8337797e0b8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389680"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>为不同网络拓扑中的 Azure Dev Spaces 配置网络

Azure Dev Spaces 在具有默认网络配置的 Azure Kubernetes 服务（AKS）群集上运行。 如果要更改 AKS 群集的网络配置，例如，将群集放在防火墙后面、使用网络安全组或使用网络策略，则必须合并运行 Azure Dev Spaces 的其他注意事项。

![虚拟网络配置](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>虚拟网络或子网配置

AKS 群集可能具有不同的虚拟网络或子网配置，以限制 AKS 群集的入口或出口流量。 例如，群集可能位于防火墙（如 Azure 防火墙）后面，或者可能使用网络安全组或自定义角色来限制网络流量。

Azure Dev Spaces 对*入口和出口*网络流量以及*仅流入*流量有一定的要求。 如果你使用的是 AKS 群集上的 Azure Dev Spaces，且该群集的虚拟网络或子网配置限制了 AKS 群集的流量，则必须遵循以下仅入站和出口流量要求，以便 Azure Dev Spaces正常运行。

### <a name="ingress-and-egress-network-traffic-requirements"></a>入口和出口网络流量要求

Azure Dev Spaces 需要以下 Fqdn 的入口和出口流量：

| FQDN                       | 端口       | 使用      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS：443 | 请求 Azure Dev Spaces 的 docker 映像 |
| gcr.io                     | HTTPS：443 | 获取 Azure Dev Spaces 的 helm 映像 |
| storage.googleapis.com     | HTTPS：443 | 获取 Azure Dev Spaces 的 helm 映像 |
| azds-* azds             | HTTPS：443 | 与 Azure Dev Spaces 控制器 Azure Dev Spaces 后端服务进行通信。 可以在*dataplaneFqdn*中找到确切的 FQDN `USERPROFILE\.azds\settings.json` |

更新防火墙或安全配置，以允许与上述所有 Fqdn 之间的网络流量。 例如，如果你使用防火墙来保护网络，则应将上述 Fqdn 添加到防火墙的应用程序规则，以允许与这些域之间的通信。

### <a name="ingress-only-network-traffic-requirements"></a>仅引入网络流量要求

Azure Dev Spaces 提供了 Kubernetes 命名空间级别的路由以及使用其自己的 FQDN 的服务的公共访问权限。 要使这两个功能正常工作，请更新防火墙或网络配置，使其能够公开进入群集上 Azure Dev Spaces 入口控制器的外部 IP 地址。 或者，你可以在防火墙中创建[内部负载均衡器][aks-internal-lb]并添加 NAT 规则，以将防火墙的公共 ip 转换为内部负载均衡器的 ip。 还可以使用[traefik][traefik-ingress]或[NGINX][nginx-ingress]创建自定义入口控制器。

## <a name="aks-cluster-network-requirements"></a>AKS 群集网络要求

通过 AKS，可以使用[网络策略][aks-network-policies]来控制群集上的 pod 和来自 pod 的出口流量之间的入口和出口流量。 Azure Dev Spaces 对*入口和出口*网络流量以及*仅流入*流量有一定的要求。 如果在具有 AKS 网络策略的 AKS 群集上使用 Azure Dev Spaces，则必须遵循以下仅入站和出口流量要求，才能正常运行 Azure Dev Spaces。

### <a name="ingress-and-egress-network-traffic-requirements"></a>入口和出口网络流量要求

Azure Dev Spaces 允许你直接与群集上的开发人员空间中的 pod 通信，以便进行调试。 要使此功能正常工作，请添加一个网络策略，该策略允许入站和出站通信与 Azure Dev Spaces 基础结构的 IP 地址，该地址[因区域而异][dev-spaces-ip-auth-range-regions]。

### <a name="ingress-only-network-traffic-requirements"></a>仅引入网络流量要求

Azure Dev Spaces 提供跨命名空间的 pod 之间的路由。 例如，启用了 Azure Dev Spaces 的命名空间可以具有父/子关系，这允许跨父命名空间和子命名空间中的 pod 路由网络流量。 要使此功能正常工作，请添加一个网络策略，该策略允许在路由网络流量的命名空间（如父/子命名空间）之间进行通信。 此外，如果将入口控制器部署到*azds*命名空间，则入口控制器需要与其他命名空间中的 Azure 开发人员空间检测到的 pod 通信。 为了使入口控制器正常工作，必须将网络流量从*azds*命名空间中允许到运行检测到的 pod 的命名空间。

## <a name="using-azure-cni"></a>使用 Azure CNI

默认情况下，AKS 群集配置为使用[kubenet][aks-kubenet]进行网络处理，这与 Azure Dev Spaces 配合使用。 还可将 AKS 群集配置为使用[Azure 容器网络接口（CNI）][aks-cni]。 若要在 AKS 群集上将 Azure Dev Spaces 与 Azure CNI 一起使用，请允许 Azure Dev Spaces 部署的虚拟网络和子网地址空间最多为10个专用 IP 地址。 有关允许专用 IP 地址的详细信息，请[参阅 AKS AZURE CNI 文档][aks-cni-ip-planning]。

## <a name="using-api-server-authorized-ip-ranges"></a>使用 API 服务器授权的 IP 范围

使用 AKS 群集，你可以配置额外的安全性，以限制哪个 IP 地址可以与你的群集进行交互，例如，使用自定义虚拟网络或[通过授权的 IP 范围保护对 API 服务器的访问][aks-ip-auth-ranges]。 若要在[创建][aks-ip-auth-range-create]群集时使用此附加安全性时使用 Azure Dev Spaces，你必须[根据你所在的区域允许其他范围][dev-spaces-ip-auth-range-regions]。 还可以[更新][aks-ip-auth-range-update]现有群集以允许其他范围。 还需要允许连接到 AKS 群集的任何开发计算机的 IP 地址进行调试，以连接到 API 服务器。

## <a name="using-aks-private-clusters"></a>使用 AKS 专用群集

目前， [AKS 专用群集][aks-private-clusters]不支持 Azure Dev Spaces。

## <a name="client-requirements"></a>客户端要求

Azure Dev Spaces 使用客户端工具（如 Azure Dev Spaces CLI 扩展、Visual Studio Code 扩展和 Visual Studio 扩展）来与 AKS 群集进行调试，以便进行调试。 若要使用 Azure Dev Spaces 的客户端工具，请允许从开发计算机到 azds.io 域 *\** 的流量。 请参阅 `USERPROFILE\.azds\settings.json` 中的*dataplaneFqdn* ，了解确切的 FQDN。 如果使用[API 服务器授权的 IP 范围][auth-range-section]，则还需要允许连接到 AKS 群集的任何开发计算机的 ip 地址进行调试，以连接到 API 服务器。

## <a name="next-steps"></a>后续步骤

了解 Azure Dev Spaces 如何帮助开发跨多个容器的更复杂应用程序，以及如何通过在不同的空间中使用不同的代码版本或分支来简化协作式开发。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 中的团队开发][team-quickstart]

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[team-quickstart]: quickstart-team-development.md
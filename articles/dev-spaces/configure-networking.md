---
title: 为不同网络拓扑中的 Azure Dev Spaces 配置网络
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: 描述在 Azure Kubernetes 服务中运行 Azure Dev Spaces 的网络要求
keywords: Azure Dev Spaces，Dev 空间，Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器，CNI，kubenet，SDN，网络
ms.openlocfilehash: 09114ab13555cbf9ef42b37c86ffb76a8fe3ab3f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91970332"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>为不同网络拓扑中的 Azure Dev Spaces 配置网络

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces 在 Azure Kubernetes Service (AKS) 具有默认网络配置的群集上运行。 如果要更改 AKS 群集的网络配置，例如，将群集放在防火墙后面、使用网络安全组或使用网络策略，则必须合并运行 Azure Dev Spaces 的其他注意事项。

![虚拟网络配置](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>虚拟网络或子网配置

AKS 群集可能具有不同的虚拟网络或子网配置，以限制 AKS 群集的入口或出口流量。 例如，群集可能位于防火墙（如 Azure 防火墙）后面，或者可能使用网络安全组或自定义角色来限制网络流量。 可在 [GitHub 上的 Azure Dev Spaces 示例存储库][sample-repo]中找到示例网络配置。

Azure Dev Spaces 对 *入口和出口* 网络流量以及 *仅流入* 流量有一定的要求。 如果你使用的是 AKS 群集上的 Azure Dev Spaces，且该群集的虚拟网络或子网配置限制了 AKS 群集的流量，则必须遵循以下仅入站和出口流量要求，才能正常运行 Azure Dev Spaces。

### <a name="ingress-and-egress-network-traffic-requirements"></a>入口和出口网络流量要求

Azure Dev Spaces 需要以下 Fqdn 的入口和出口流量：

| FQDN                       | 端口       | 用途      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS：443 | 请求 Azure Dev Spaces 的 docker 映像 |
| gcr.io                     | HTTPS：443 | 获取 Azure Dev Spaces 的 helm 映像 |
| storage.googleapis.com     | HTTPS：443 | 获取 Azure Dev Spaces 的 helm 映像 |

更新防火墙或安全配置，以允许与上述所有 Fqdn 和 [Azure Dev Spaces 基础结构服务][service-tags]的网络流量。 例如，如果你使用防火墙来保护网络，则应将上述 Fqdn 添加到防火墙的应用程序规则中，并且还必须将 Azure Dev Spaces 服务标记 [添加到防火墙][firewall-service-tags]。 需要对防火墙进行这两项更新，以允许进出这些域的流量。

### <a name="ingress-only-network-traffic-requirements"></a>仅引入网络流量要求

Azure Dev Spaces 提供了 Kubernetes 命名空间级别的路由以及使用其自己的 FQDN 的服务的公共访问权限。 要使这两个功能正常工作，请更新防火墙或网络配置，使其能够公开进入群集上 Azure Dev Spaces 入口控制器的外部 IP 地址。 或者，你可以在防火墙中创建 [内部负载均衡器][aks-internal-lb] 并添加 NAT 规则，以将防火墙的公共 ip 转换为内部负载均衡器的 ip。 还可以使用 [traefik][traefik-ingress] 或 [NGINX][nginx-ingress] 创建自定义入口控制器。

## <a name="aks-cluster-network-requirements"></a>AKS 群集网络要求

通过 AKS，可以使用 [网络策略][aks-network-policies] 来控制群集上的 pod 和来自 pod 的出口流量之间的入口和出口流量。 Azure Dev Spaces 对 *入口和出口* 网络流量以及 *仅流入* 流量有一定的要求。 如果在具有 AKS 网络策略的 AKS 群集上使用 Azure Dev Spaces，则必须遵循以下仅入站和出口流量要求，才能正常运行 Azure Dev Spaces。

### <a name="ingress-and-egress-network-traffic-requirements"></a>入口和出口网络流量要求

Azure Dev Spaces 允许你直接与群集上的开发人员空间中的 pod 通信，以便进行调试。 要使此功能正常工作，请添加一个网络策略，该策略允许入站和出站通信与 Azure Dev Spaces 基础结构的 IP 地址，该地址 [因区域而异][service-tags]。

### <a name="ingress-only-network-traffic-requirements"></a>仅引入网络流量要求

Azure Dev Spaces 提供跨命名空间的 pod 之间的路由。 例如，启用了 Azure Dev Spaces 的命名空间可以具有父/子关系，这允许跨父命名空间和子命名空间中的 pod 路由网络流量。 Azure Dev Spaces 也使用其自己的 FQDN 公开服务终结点。 若要配置公开服务的不同方式，以及它如何影响命名空间级别的路由，请参阅 [使用不同的终结点选项][endpoint-options]。

## <a name="using-azure-cni"></a>使用 Azure CNI

默认情况下，AKS 群集配置为使用 [kubenet][aks-kubenet] 进行网络处理，这与 Azure Dev Spaces 配合使用。 你还可以将 AKS 群集配置为使用 [Azure Container 网络接口 (CNI) ][aks-cni]。 若要在 AKS 群集上将 Azure Dev Spaces 与 Azure CNI 一起使用，请允许 Azure Dev Spaces 部署的虚拟网络和子网地址空间最多为10个专用 IP 地址。 有关允许专用 IP 地址的详细信息，请 [参阅 AKS AZURE CNI 文档][aks-cni-ip-planning]。

## <a name="using-api-server-authorized-ip-ranges"></a>使用 API 服务器授权的 IP 范围

使用 AKS 群集，你可以配置额外的安全性，以限制哪个 IP 地址可以与你的群集进行交互，例如，使用自定义虚拟网络或 [通过授权的 IP 范围保护对 API 服务器的访问][aks-ip-auth-ranges]。 若要在 [创建][aks-ip-auth-range-create] 群集时使用此附加安全性时使用 Azure Dev Spaces，你必须 [根据你所在的区域允许其他范围][service-tags]。 还可以 [更新][aks-ip-auth-range-update] 现有群集以允许其他范围。 还需要允许连接到 AKS 群集的任何开发计算机的 IP 地址进行调试，以连接到 API 服务器。

## <a name="using-aks-private-clusters"></a>使用 AKS 专用群集

目前， [AKS 专用群集][aks-private-clusters]不支持 Azure Dev Spaces。

## <a name="using-different-endpoint-options"></a>使用不同的终结点选项

Azure Dev Spaces 可以选择公开在 AKS 上运行的服务的终结点。 启用群集上的 Azure Dev Spaces 时，可以使用以下选项配置群集的终结点类型：

* 默认情况下， *公用* 终结点使用公共 IP 地址部署入口控制器。 公共 IP 地址在群集的 DNS 上注册，允许使用 URL 对服务进行公共访问。 您可以使用查看此 URL `azds list-uris` 。
* *专用*终结点使用专用 IP 地址部署入口控制器。 使用专用 IP 地址，可以从群集的虚拟网络内部访问群集的负载均衡器。 负载均衡器的专用 IP 地址在群集的 DNS 上注册，以便可以使用 URL 访问群集的虚拟网络中的服务。 您可以使用查看此 URL `azds list-uris` 。
* 如果为 endpoint 选项设置 *none* ，则不会部署入口控制器。 如果未部署入口控制器， [Azure Dev Spaces 路由功能][dev-spaces-routing] 将不起作用。 或者，你可以使用 [traefik][traefik-ingress] 或 [NGINX][nginx-ingress]实现你自己的入口控制器解决方案，这将允许路由功能重新工作。

若要配置终结点选项，请在群集上启用 Azure Dev Spaces 时使用 *-e* 或 *--endpoint* 。 例如：

> [!NOTE]
> Endpoint 选项要求 Azure CLI 版本2.2.0 或更高版本运行。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>客户端要求

Azure Dev Spaces 使用客户端工具（如 Azure Dev Spaces CLI 扩展、Visual Studio Code 扩展和 Visual Studio 扩展）来与 AKS 群集进行调试，以便进行调试。 若要使用 Azure Dev Spaces 的客户端工具，请允许从开发计算机到 [Azure Dev Spaces 基础结构][dev-spaces-allow-infrastructure]的流量。 如果使用 [API 服务器授权的 IP 范围][auth-range-section]，则还需要允许连接到 AKS 群集的任何开发计算机的 ip 地址进行调试，以连接到 API 服务器。

## <a name="next-steps"></a>后续步骤

深入了解 Azure Dev Spaces 的工作方式。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 工作原理](how-dev-spaces-works.md)

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
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-allow-infrastructure]: #virtual-network-or-subnet-configurations
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[firewall-service-tags]: ../firewall/service-tags.md
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[service-tags]: ../virtual-network/service-tags-overview.md#available-service-tags
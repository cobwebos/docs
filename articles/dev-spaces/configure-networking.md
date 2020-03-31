---
title: 为不同网络拓扑中的 Azure 开发空间配置网络
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: 描述在 Azure 库伯奈斯服务中运行 Azure 开发空间的网络要求
keywords: Azure 开发空间、开发空间、Docker、库伯奈斯、Azure、AKS、Azure 库伯奈斯服务、容器、CNI、kubenet、SDN、网络
ms.openlocfilehash: 82d046aa36fe9caf6337aa7f58ca0db525062283
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240575"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>为不同网络拓扑中的 Azure 开发空间配置网络

Azure 开发空间在具有默认网络配置的 Azure 库伯奈斯服务 （AKS） 群集上运行。 如果要更改 AKS 群集的网络配置（例如将群集置于防火墙后、使用网络安全组或使用网络策略），则必须合并运行 Azure Dev Space 的其他注意事项。

![虚拟网络配置](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>虚拟网络或子网配置

AKS 群集可能具有不同的虚拟网络或子网配置，以限制 AKS 群集的入口或出口流量。 例如，群集可能落后于防火墙（如 Azure 防火墙），或者您可以使用网络安全组或自定义角色来限制网络流量。

Azure 开发空间对*入口和出口*网络流量以及*仅入口*流量有某些要求。 如果在 AKS 群集上使用 Azure 开发人员空间，其虚拟网络或子网配置限制 AKS 群集的流量，则必须仅遵循以下入口和入口和出口流量要求，以便 Azure 开发空间功能正常。

### <a name="ingress-and-egress-network-traffic-requirements"></a>入口和出口网络流量要求

Azure 开发空间需要以下 FQDN 的入口和出口流量：

| FQDN                       | 端口       | 用途      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS：443 | 为 Azure 开发空间提取 Docker 映像 |
| gcr.io                     | HTTPS：443 | 为 Azure 开发空间拉舵映像 |
| storage.googleapis.com     | HTTPS：443 | 为 Azure 开发空间拉舵映像 |
| 阿兹兹-*.azds.io             | HTTPS：443 | 与 Azure 开发人员空间控制器的 Azure 开发空间后端服务进行通信。 确切的 FQDN 可在*数据平面Fqdn*中找到。`USERPROFILE\.azds\settings.json` |

更新防火墙或安全配置，以允许网络流量与上述所有 FQDN 进行传输。 例如，如果您使用防火墙来保护网络，则应将上述 FQDN 添加到防火墙的应用程序规则中，以允许流量来自这些域。

### <a name="ingress-only-network-traffic-requirements"></a>仅入口网络流量要求

Azure 开发空间提供 Kubernetes 命名空间级路由以及使用其自己的 FQDN 对服务的公共访问。 要使这两个功能都正常工作，请更新防火墙或网络配置，以允许公共进入群集上的 Azure Dev Space 入口控制器的外部 IP 地址。 或者，您可以创建[内部负载均衡器][aks-internal-lb]并在防火墙中添加 NAT 规则，将防火墙的公共 IP 转换为内部负载均衡器的 IP。 您还可以使用[traefik][traefik-ingress]或[NGINX][nginx-ingress]创建自定义入口控制器。

## <a name="aks-cluster-network-requirements"></a>AKS 群集网络要求

AKS 允许您使用[网络策略][aks-network-policies]来控制群集上的 Pod 之间的入口和出口流量以及来自 Pod 的出入口流量。 Azure 开发空间对*入口和出口*网络流量以及*仅入口*流量有某些要求。 如果在 AKS 群集上使用 Azure 开发人员空间，并且使用 AKS 网络策略，则必须仅遵循以下入口以及入口和出口流量要求，以便 Azure 开发空间正常运行。

### <a name="ingress-and-egress-network-traffic-requirements"></a>入口和出口网络流量要求

Azure 开发人员空间允许您直接与群集上的开发空间中的窗格进行通信以进行调试。 要此功能正常工作，添加一个网络策略，允许入口和传出通信到 Azure 开发人员空间基础结构的 IP 地址，[该地址因区域而异][dev-spaces-ip-auth-range-regions]。

### <a name="ingress-only-network-traffic-requirements"></a>仅入口网络流量要求

Azure 开发空间提供跨命名空间的窗格之间的路由。 例如，启用 Azure 开发人员空间的命名空间可以具有父/子关系，这允许在父和子命名空间的 pod 之间路由网络流量。 Azure 开发空间还使用其自己的 FQDN 公开服务终结点。 要配置不同的公开服务的方式及其如何影响命名空间级别路由，请参阅[使用不同的终结点选项][endpoint-options]。

## <a name="using-azure-cni"></a>使用 Azure CNI

默认情况下，AKS 群集配置为将[kubenet][aks-kubenet]用于网络，该群集适用于 Azure 开发空间。 还可以将 AKS 群集配置为使用[Azure 容器网络接口 （CNI）。][aks-cni] 要在 AKS 群集上使用 Azure 开发人员空间与 Azure CNI 一起使用，请允许虚拟网络和子网地址空间最多 10 个专用 IP 地址，用于 Azure 开发空间部署的 Pod。 有关允许专用 IP 地址的更多详细信息，请参阅[AKS Azure CNI 文档][aks-cni-ip-planning]。

## <a name="using-api-server-authorized-ip-ranges"></a>使用 API 服务器授权的 IP 范围

AKS 群集允许您配置其他安全性，以限制哪些 IP 地址可以与群集交互，例如使用自定义虚拟网络或使用[授权的 IP 范围保护对 API 服务器的访问][aks-ip-auth-ranges]。 要在[创建][aks-ip-auth-range-create]群集时使用此附加安全性时使用 Azure 开发人员空间，必须[允许基于区域 的其他范围][dev-spaces-ip-auth-range-regions]。 您还可以[更新][aks-ip-auth-range-update]现有群集以允许这些其他范围。 您还需要允许连接到 AKS 群集的任何开发计算机的 IP 地址进行调试以连接到 API 服务器。

## <a name="using-aks-private-clusters"></a>使用 AKS 专用群集

此时[，AKS 专用群集][aks-private-clusters]不支持 Azure 开发空间。

## <a name="using-different-endpoint-options"></a>使用不同的终结点选项

Azure 开发空间可以选择公开在 AKS 上运行的服务的终结点。 在群集上启用 Azure 开发空间时，有以下选项用于配置群集的终结点类型：

* *公共*终结点（默认值为默认终结点）部署具有公共 IP 地址的入口控制器。 公共 IP 地址在群集的 DNS 上注册，允许使用 URL 公开访问您的服务。 您可以使用 查看此 URL。 `azds list-uris`
* *专用*终结点部署具有专用 IP 地址的入口控制器。 使用专用 IP 地址时，只能通过群集的虚拟网络内部访问群集的负载均衡器。 负载均衡器的专用 IP 地址在群集的 DNS 上注册，以便可以使用 URL 访问群集虚拟网络中的服务。 您可以使用 查看此 URL。 `azds list-uris`
* 为终结点选项设置 *"无"* 会导致不部署入口控制器。 未部署入口控制器[，Azure 开发空间路由功能][dev-spaces-routing]将不起作用。 或者，您可以使用[traefik][traefik-ingress]或[NGINX][nginx-ingress]实现您自己的入口控制器解决方案，这将允许路由功能再次工作。

要配置终结点选项，在群集上启用 Azure 开发空间时，请使用 *-e*或 *--终结点*。 例如：

> [!NOTE]
> 终结点选项要求您运行 Azure CLI 版本 2.2.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>客户端要求

Azure 开发空间使用客户端工具（如 Azure 开发空间 CLI 扩展、可视化工作室代码扩展和可视化工作室扩展）与 AKS 群集进行调试。 要使用 Azure 开发人员空间客户端工具，请允许从开发计算机到*azds-.azds.io\** 域的流量。 有关确切的`USERPROFILE\.azds\settings.json`*FQDN，请参阅数据平面Fqdn。* 如果使用[API 服务器授权的 IP 范围][auth-range-section]，还需要允许连接到 AKS 群集的任何开发计算机的 IP 地址进行调试以连接到 API 服务器。

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
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[team-quickstart]: quickstart-team-development.md
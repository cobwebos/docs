---
title: 常见问题 Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: 查找有关 Azure Dev Spaces 的一些常见问题的解答
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
ms.openlocfilehash: 7439af9c5f936d309df655ca6fa301c39fa3f9ec
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117792"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>常见问题 Azure Dev Spaces

这解决了有关 Azure Dev Spaces 的常见问题。

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>哪些 Azure 区域当前提供 Azure Dev Spaces？

有关可用区域的完整列表，请参阅[支持的区域][supported-regions]。

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>是否可以将 Azure Dev Spaces 的 AKS 群集迁移到另一个区域？

是，如果你想要将 AKS 群集与 Azure Dev Spaces 移动到另一个[受支持的区域][supported-regions]，则建议你在另一个区域中创建新群集，然后安装并配置 Azure Dev Spaces 并将资源和应用程序部署到新群集。 有关迁移 AKS 的详细信息，请参阅[迁移到 Azure Kubernetes 服务（AKS）][aks-migration]。

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>能否在现有 Dockerfile 或 Helm 图表中使用 Azure Dev Spaces？

是的，如果你的项目已有 Dockerfile 或 Helm 图表，则可以将这些文件与 Azure Dev Spaces 一起使用。 运行 `azds prep`时，请使用 `--chart` 参数并指定图表的位置。 Azure Dev Spaces 仍将生成*yaml*和*Dockerfile*文件，但不会替换或修改现有 Dockerfile 或 Helm 图表。 你可能需要修改*azds*文件和*Dockerfile*文件，以便在 `azds up`运行时，所有内容都能正常使用现有应用程序。

使用自己的 Dockerfile 或 Helm 图表时，有以下限制：
* 如果只使用一个 Dockerfile，它必须包含启用开发方案所需的所有内容，例如语言 SDK，而不只是运行时。 如果对 Azure Dev Spaces 使用单独的 Dockerfile （如 Dockerfile），则必须在该 Dockerfile 中包含启用开发方案所需的所有内容。
* Helm 图表必须支持将部分或整个图像标记作为值从*yaml*。
* 如果你要使用入口来修改任何内容，则还可以更新 Helm 图表以使用 Azure Dev Spaces 提供的入口解决方案。
* 如果要使用[Azure Dev Spaces 提供的路由功能][dev-spaces-routing]，则单个项目的所有服务都必须在单个 Kubernetes 命名空间中，并且必须使用简单命名进行部署，例如*服务-a*。 在标准 Helm 的图表中，可以通过指定*fullnameOverride*属性的值来完成此命名更新。

若要将自己的 Dockerfile 或 Helm 图表与 Azure Dev Spaces 使用的现有版本进行比较，请查看[快速入门][quickstart-cli]中生成的文件。


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>能否修改 Azure Dev Spaces 生成的文件？

是的，你可以修改[Azure Dev Spaces 在准备项目时生成][dev-spaces-prep]的*Yaml*文件、Dockerfile 和 Helm 图表。 修改这些文件将更改生成和运行项目的方式。

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>是否可以使用没有公共 IP 地址的 Azure Dev Spaces？

不能，无法在没有公共 IP 的 AKS 群集上预配 Azure Dev Spaces。 [Azure Dev Spaces 路由需要][dev-spaces-routing]公共 IP。

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>能否将我自己的入口用于 Azure Dev Spaces？

是的，你可以配置自己的入口 Azure Dev Spaces 创建。 例如，可以使用[traefik][ingress-traefik]或[NGINX][ingress-nginx]。

## <a name="can-i-use-https-with-azure-dev-spaces"></a>是否可以将 HTTPS 与 Azure Dev Spaces 一起使用？

是的，你可以使用[traefik][ingress-https-traefik]或[NGINX][ingress-https-nginx]配置你自己的入口和 HTTPS。

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>能否在使用 CNI 而不是 kubenet 的群集上使用 Azure Dev Spaces？ 

是的，可以在使用 CNI 进行网络连接的 AKS 群集上使用 Azure Dev Spaces。 例如，可以在具有[现有 Windows 容器][windows-containers]的 AKS 群集上使用 Azure Dev Spaces，使用 CNI 进行网络处理。 [此处](configure-networking.md#using-azure-cni)提供有关将 CNI 与 Azure Dev Spaces 配合使用的详细信息。

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>是否可以将 Azure Dev Spaces 与 Windows 容器一起使用？

目前，Azure Dev Spaces 仅在 Linux pod 和节点上运行，但你可以使用[现有的 Windows 容器][windows-containers]在 AKS 群集上运行 Azure Dev Spaces。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>能否在启用 API 服务器授权 IP 地址范围的 AKS 群集上使用 Azure Dev Spaces？

是的，你可以使用启用了[API 服务器授权 IP 地址范围][aks-auth-range]的 AKS 群集上的 Azure Dev Spaces。 [此处](configure-networking.md#using-api-server-authorized-ip-ranges)提供了有关使用 AZURE DEV SPACES 启用 API 服务器授权 IP 地址范围的 AKS 群集的详细信息。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>能否在群集节点的受限制传出流量的 AKS 群集上使用 Azure Dev Spaces？

是的，一旦允许使用正确的 Fqdn，你就可以在具有[受限制的群集节点流量的][aks-restrict-egress-traffic]AKS 群集上使用 Azure Dev Spaces。 若要详细了解如何对启用了 Azure Dev Spaces 的群集节点使用具有受限传出流量的 AKS[群集。](configure-networking.md#ingress-and-egress-network-traffic-requirements)

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>能否在启用 RBAC 的 AKS 群集上使用 Azure Dev Spaces？

是的，可以在启用了 RBAC 的情况下，在 AKS 群集上使用 Azure Dev Spaces。

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>在 Visual Studio 中为项目启用入口后会出现什么情况？

使用 Visual Studio 准备项目时，可以选择为服务启用入口。 启用入口会创建一个公共终结点，以便在 AKS 群集上运行时访问该服务，这是可选的。 如果不启用入口，则只能从 AKS 群集内访问服务。

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>是否可以将 pod 托管标识与 Azure Dev Spaces 一起使用？

目前，Azure Dev Spaces 不支持在启用了 Azure Dev Spaces 的 AKS 群集上使用[pod 托管标识][aks-pod-managed-id]。 如果安装了 pod 托管标识，并想要将其卸载，可以在[卸载说明][aks-pod-managed-id-uninstall]中找到更多详细信息。

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-prep]: how-dev-spaces-works.md#prepare-your-code
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md
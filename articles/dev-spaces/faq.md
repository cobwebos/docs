---
title: 有关 Azure 开发空间的常见问题
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: 查找有关 Azure 开发空间的一些常见问题的解答
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
ms.openlocfilehash: f3ac6ec3c3ddbe8ff508befba2eb4a8423e66f07
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998735"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>有关 Azure 开发空间的常见问题

这解决了有关 Azure 开发空间的常见问题。

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>哪些 Azure 区域当前提供 Azure 开发空间？

有关可用区域的完整列表，请参阅[支持的区域][supported-regions]。

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>是否可以将具有 Azure 开发空间的 AKS 群集迁移到其他区域？

是的，如果要将具有 Azure 开发人员空间的 AKS 群集移动到其他[受支持的区域][supported-regions]，我们建议您在其他区域中创建新群集，然后安装和配置 Azure 开发空间，并将资源和应用程序部署到新群集。 有关迁移 AKS 的详细信息，请参阅[迁移到 Azure 库伯奈斯服务 （AKS）。][aks-migration]

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>是否可以将 Azure 开发人员空间与现有的 Dockerfile 或 Helm 图表一起使用？

是，如果项目已具有 Dockerfile 或 Helm 图表，则可以使用这些文件与 Azure 开发空间一起。 运行`azds prep`时，请使用 参数`--chart`并指定图表的位置。 Azure 开发人员空间仍将生成*azds.yaml*和*Dockerfile.开发*文件，但不会替换或修改现有的 Dockerfile 或 Helm 图表。 您可能需要修改*azds.yaml*和*Dockerfile.开发*文件，以便在运行时`azds up`使所有内容都与现有应用程序正确配合使用。

使用您自己的 Dockerfile 或 Helm 图表时，有以下限制：
* 如果仅使用一个 Dockerfile，它必须包含启用开发方案所需的一切，例如语言 SDK，而不仅仅是运行时。 如果对 Azure 开发人员空间（如 Dockerfile.开发）使用单独的 Dockerfile，则启用开发方案所需的一切必须包含在该 Dockerfile 中。
* Helm 图表必须支持将部分或整个图像标记作为*值从值*传递。
* 如果要使用入口修改任何内容，还可以更新 Helm 图表以使用 Azure 开发人员空间提供的入口解决方案。
* 如果要使用[Azure 开发人员空间提供的路由功能][dev-spaces-routing]，则单个项目的所有服务必须适合单个 Kubernetes 命名空间，并且必须使用简单的命名（例如*服务 -a）* 进行部署。 在标准 Helm 图表中，可以通过指定*全名覆盖*属性的值来完成此命名更新。

要将您自己的 Dockerfile 或 Helm 图表与适用于 Azure 开发人员空间的现有版本进行比较，请查看[快速入门][quickstart-cli]中生成的文件。


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>是否可以修改 Azure 开发空间生成的文件？

可以，您可以在[准备项目时修改 Azure 开发人员空间生成的][dev-spaces-prep] *azds.yaml*文件、Dockerfile 和 Helm 图表。 修改这些文件会更改项目的生成和运行方式。

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>是否可以在没有公共 IP 地址的情况下使用 Azure 开发空间？

否，如果没有公共 IP，无法在 AKS 群集上预配 Azure 开发空间。 [Azure 开发空间需要][dev-spaces-routing]公共 IP 才能进行路由。

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>是否可以使用自己的入口与 Azure 开发空间？

可以，您可以沿入口 Azure 开发人员空间创建的一侧配置自己的入口。 例如，您可以使用[曲解][ingress-traefik]或[NGINX][ingress-nginx]。

## <a name="can-i-use-https-with-azure-dev-spaces"></a>是否可以将 HTTPS 与 Azure 开发空间一起使用？

是的，您可以使用[traefik][ingress-https-traefik]或[NGINX][ingress-https-nginx]使用 HTTPS 配置自己的入口。

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>是否可以在使用 CNI 而不是 kubenet 的群集上使用 Azure 开发空间？ 

可以，您可以在使用 CNI 进行网络化的 AKS 群集上使用 Azure 开发空间。 例如，可以将 AKS 群集上的 Azure 开发空间与[现有的 Windows 容器][windows-containers]一起使用，该容器使用 CNI 进行网络化。 有关使用 CNI 与 Azure 开发空间联网的详细信息，[请访问此处](configure-networking.md#using-azure-cni)。

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>是否可以将 Azure 开发空间与 Windows 容器一起使用？

目前，Azure 开发人员空间仅用于 Linux pod 和节点，但您可以在具有[现有 Windows 容器][windows-containers]的 AKS 群集上运行 Azure 开发人员空间。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>启用 API 服务器授权的 IP 地址范围后，是否可以在 AKS 群集上使用 Azure 开发空间？

可以，您可以在启用[API 服务器授权的 IP 地址范围][aks-auth-range]的 AKS 群集上使用 Azure 开发空间。 有关使用具有 AZURE 开发空间启用的 API 服务器授权的 IP 地址范围的 AKS 群集的详细信息[，可在此处获取](configure-networking.md#using-api-server-authorized-ip-ranges)。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>是否可以在具有群集节点出口流量受限的 AKS 群集上使用 Azure 开发空间？

可以，在允许正确的 FQDN 后，可以在 AKS 群集上使用 Azure 开发空间，对[群集节点启用受限出口流量][aks-restrict-egress-traffic]。 有关使用具有受限出口流量的 AKS 群集，以及使用 Azure 开发空间启用的群集节点的详细信息[，可在此处获取](configure-networking.md#ingress-and-egress-network-traffic-requirements)。

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>是否可以在启用 RBAC 的 AKS 群集上使用 Azure 开发空间？

可以，您可以在启用或未启用 RBAC 的 AKS 群集上使用 Azure 开发空间。

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>当我在 Visual Studio 中为项目启用入口时会发生什么情况？

使用 Visual Studio 准备项目时，您可以选择为服务启用入口。 启用入口将创建一个公共终结点，用于在 AKS 群集上运行时访问服务，这是可选的。 如果不启用入口，则服务只能通过 AKS 群集内访问。

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>是否可以使用具有 Azure 开发空间的窗格托管标识？

目前，Azure 开发人员空间不支持在启用 Azure 开发空间的 AKS 群集上使用[pod 托管标识][aks-pod-managed-id]。 如果安装了 pod 托管标识，并希望卸载它，则可以在[卸载说明][aks-pod-managed-id-uninstall]中找到更多详细信息。

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>是否可以将 Azure 开发人员空间与应用程序中的多个微服务一起使用？

可以，可以在具有多个微服务的应用程序中使用 Azure 开发人员空间，但必须在其根目录上准备和运行各个微服务。 Azure 开发空间 CLI、Azure 开发空间 VS 代码扩展和可视化工作室 Azure 开发工作负荷期望*azds.yaml*文件位于微服务的根目录，以便运行和调试。 有关单个应用程序中多个微服务的示例，请参阅[自行车共享示例应用程序][bike-sharing]。

在可视化工作室代码中，可以在[单个工作区中打开单独的项目][vs-code-multi-root-workspaces]，并通过 Azure 开发空间单独调试这些项目。 每个项目都必须是自包含的，并为 Azure 开发空间做好准备。

在可视化工作室中，可以配置 .NET 核心解决方案，以便通过 Azure 开发空间进行调试。

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md
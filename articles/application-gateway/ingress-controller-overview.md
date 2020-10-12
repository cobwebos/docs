---
title: 什么是 Azure 应用程序网关入口控制器？
description: 本文简单介绍了什么是应用程序网关入口控制器。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: c1bd41587e4f56fb0a7f3eb8285d301751f558d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84668094"
---
# <a name="what-is-application-gateway-ingress-controller"></a>什么是应用程序网关入口控制器？
应用程序网关入口控制器 (AGIC) 是一个 Kubernetes 应用程序。有了它，[Azure Kubernetes 服务 (AKS)](https://azure.microsoft.com/services/kubernetes-service/) 客户就可以利用 Azure 的本机[应用程序网关](https://azure.microsoft.com/services/application-gateway/) L7 负载均衡器向 Internet 公开云软件。 AGIC 监视托管时所在的 Kubernetes 群集并持续更新应用程序网关，以便向 Internet 公开所选服务。

在客户的 AKS 上，入口控制器在其自己的 Pod 中运行。 AGIC 监视部分 Kubernetes 资源中的更改。 AKS 群集的状态会转换为特定于应用程序网关的配置并应用到 [Azure 资源管理器 (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。

## <a name="benefits-of-application-gateway-ingress-controller"></a>应用程序网关入口控制器的好处
有了 AGIC，就不需在 AKS 群集前面设置另一个负载均衡器/公共 IP，避免在请求到达 AKS 群集之前在数据路径中设置多个跃点。 应用程序网关直接使用其专用 IP 与 Pod 通信，不需要 NodePort 或 KubeProxy 服务。 这也会改进部署性能。

入口控制器完全由 Standard_v2 和 WAF_v2 SKU 提供支持，这也会带来自动缩放的好处。 应用程序网关可以响应流量负载的增减并相应地进行缩放，不消耗 AKS 群集中的任何资源。

在 AGIC 基础上使用应用程序网关还可以提供 TLS 策略和 Web 应用程序防火墙 (WAF) 功能，这有助于保护 AKS 群集。

![Azure 应用程序网关 + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC 通过 Kubernetes [入口资源](https://kubernetes.io/docs/user-guide/ingress/)以及服务和部署/Pod 进行配置。 它提供许多功能，利用 Azure 的本机应用程序网关 L7 负载均衡器。 例如：
  - URL 路由
  - 基于 Cookie 的相关性
  - TLS 终止
  - 端到端 TLS
  - 支持公共、专用和混合网站
  - 集成式 Web 应用程序防火墙

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>Helm 部署与 AKS 加载项之间的差异
可通过两种方式为 AKS 群集部署 AGIC。 第一种方法是通过 Helm；第二种方式是通过 AKS 作为加载项部署。 将 AGIC 部署为 AKS 加载项的主要好处是，它比通过 Helm 部署要简单得多。 若要进行新的设置，在 Azure CLI 中通过一行内容即可部署新应用程序网关和将 AGIC 作为加载项启用的新 AKS 群集。 该加载项也是一项完全托管的服务，这提供了一些额外的优势，例如自动更新和更多的支持。 AKS 不支持通过 Helm 部署的 AGIC，但是 AKS 支持将 AGIC 作为 AKS 的加载项部署。 

AGIC 加载项在客户的 AKS 群集中仍作为 pod 部署，但是 Helm 部署版本与加载项版本的 AGIC 之间存在一些差异。 下面列出了两个版本之间的差异： 
  - 无法在 AKS 加载项上修改 Helm 部署值：
    - `verbosityLevel` 默认设置为 5
    - `usePrivateIp` 默认设置为 false，但可由 [use-private-ip annotation](ingress-controller-annotations.md#use-private-ip) 重写此项
    - 加载项不支持 `shared` 
    - 加载项不支持 `reconcilePeriodSeconds`
    - 加载项不支持 `armAuth.type`
  - 通过 Helm 部署的 AGIC 支持 ProhibitedTargets，这意味着 AGIC 可以专为 AKS 群集配置应用程序网关，不影响其他现有的后端。 AGIC 加载项目前不支持此项。 
  - 由于 AGIC 加载项是一种托管服务，因此客户的 AGIC 加载项将自动更新为最新版本，不像通过 Helm 部署的 AGIC，客户必须手动更新 AGIC。 

> [!NOTE]
> AGIC AKS 加载项部署方法目前处于预览状态。 不建议在仍处于预览状态的功能上运行生产工作负载，如果需要试用，建议设置新的群集并使用这些功能进行测试。 

下表按 AGIC 的 Helm 部署版本和 AKS 加载项版本当前支持的方案进行分类。 

### <a name="aks-add-on-agic-single-aks-cluster"></a>AKS 加载项 AGIC（单一 AKS 群集）
|                  |1 个应用程序网关 |2+ 个应用程序网关 |
|------------------|---------|--------|
|**1 个 AGIC**|是，支持 |否，这还在我们的积压工作 (backlog) 中 |
|**2+ 个 AGIC**|否，每个群集仅支持 1 个 AGIC |否，每个群集仅支持 1 个 AGIC |

### <a name="helm-deployed-agic-single-aks-cluster"></a>通过 Helm 部署的 AGIC（单一 AKS 群集）
|                  |1 个应用程序网关 |2+ 个应用程序网关 |
|------------------|---------|--------|
|**1 个 AGIC**|是，支持 |否，这还在我们的积压工作 (backlog) 中 |
|**2+ 个 AGIC**|必须使用共享的 ProhibitedTarget 功能，并监视各单独的命名空间 |是，支持 |

### <a name="helm-deployed-agic-2-aks-clusters"></a>通过 Helm 部署的 AGIC（2+ 个 AKS 群集）
|                  |1 个应用程序网关 |2+ 个应用程序网关 |
|------------------|---------|--------|
|**1 个 AGIC**|空值 |空值 |
|**2+ 个 AGIC**|必须使用共享的 ProhibitedTarget 功能 |空值 |

## <a name="next-steps"></a>后续步骤
- [**AKS 加载项“绿色地带”部署**](tutorial-ingress-controller-add-on-new.md)：有关如何在白板基础结构上安装 AGIC 加载项、AKS 和应用程序网关的说明。
- [**AKS 加载项“棕色地带”部署**](tutorial-ingress-controller-add-on-existing.md)：使用现有的应用程序网关在 AKS 群集上安装 AGIC 加载项。
- [**Helm“绿色地带”部署**](ingress-controller-install-new.md)：在白板基础结构上通过 Helm、新 AKS 群集和新应用程序网关安装 AGIC。
- [**Helm“棕色地带”部署**](ingress-controller-install-existing.md)：在现有的 AKS 群集和应用程序网关上通过 Helm 部署 AGIC。


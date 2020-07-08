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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668094"
---
# <a name="what-is-application-gateway-ingress-controller"></a>什么是应用程序网关入口控制器？
应用程序网关入口控制器 (AGIC) 是一个 Kubernetes 应用程序。有了它，[Azure Kubernetes 服务 (AKS)](https://azure.microsoft.com/services/kubernetes-service/) 客户就可以利用 Azure 的本机[应用程序网关](https://azure.microsoft.com/services/application-gateway/) L7 负载均衡器向 Internet 公开云软件。 AGIC 监视托管时所在的 Kubernetes 群集并持续更新应用程序网关，以便向 Internet 公开所选服务。

在客户的 AKS 上，入口控制器在其自己的 Pod 中运行。 AGIC 监视部分 Kubernetes 资源中的更改。 AKS 群集的状态会转换为特定于应用程序网关的配置并应用到 [Azure 资源管理器 (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。

## <a name="benefits-of-application-gateway-ingress-controller"></a>应用程序网关入口控制器的好处
AGIC 帮助消除了在 AKS 群集前面有其他负载平衡器/公共 IP 的需要，并在请求到达 AKS 群集之前避免了数据路径中的多个跃点。 应用程序网关直接使用其专用 IP 与 Pod 通信，不需要 NodePort 或 KubeProxy 服务。 这也会改进部署性能。

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

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>Helm 部署与 AKS 外接程序之间的差异
可以通过两种方式为 AKS 群集部署 AGIC。 第一种方法是通过 Helm;第二种方式是通过 AKS 作为外接程序。 将 AGIC 部署为 AKS 外接程序的主要好处是，它比通过 Helm 部署要简单得多。 对于新的设置，可以部署新的应用程序网关和启用了 AGIC 的新 AKS 群集，并将其作为外接程序安装在 Azure CLI 的一行中。 该外接程序也是一项完全托管的服务，它提供了一些额外的优势，例如自动更新和更高的支持。 AKS 不支持通过 Helm 部署的 AGIC，但是，AKS 支持作为 AKS 外接程序部署的 AGIC。 

AGIC 外接程序仍作为 pod 部署在客户的 AKS 群集中，但是，Helm 部署版本与 AGIC 的外接程序版本之间存在一些差异。 下面列出了两个版本之间的差异： 
  - 无法在 AKS 外接程序上修改 Helm 部署值：
    - `verbosityLevel`默认情况下将设置为5
    - `usePrivateIp`默认情况下，将设置为 false;这可以被[使用-专用-ip 批注](ingress-controller-annotations.md#use-private-ip)覆盖
    - `shared`外接程序不支持 
    - `reconcilePeriodSeconds`外接程序不支持
    - `armAuth.type`外接程序不支持
  - 通过 Helm 部署的 AGIC 支持 ProhibitedTargets，这意味着 AGIC 可以专门为 AKS 群集配置应用程序网关，而不会影响其他现有的后端。 AGIC 外接程序当前不支持此项。 
  - 由于 AGIC 外接程序是一种托管服务，因此，客户将自动更新为最新版本的 AGIC 外接程序，这不同于通过 Helm 部署的 AGIC，客户必须手动更新 AGIC。 

> [!NOTE]
> 部署的 AGIC AKS 附加方法目前处于预览阶段。 我们不建议在仍处于预览状态的功能上运行生产工作负荷，因此，如果你想要试用它，我们建议设置新的群集，以便使用对其进行测试。 

下表对 Helm 部署版本和 AGIC 的 AKS 外接程序版本当前支持的方案进行排序。 

### <a name="aks-add-on-agic-single-aks-cluster"></a>AKS 外接程序 AGIC （单一 AKS 群集）
|                  |1个应用程序网关 |2 + 应用程序网关 |
|------------------|---------|--------|
|**1 AGIC**|是的，支持 |不是，这是我们的积压工作 |
|**2 + AGICs**|否，仅支持1个 AGIC/群集 |否，仅支持1个 AGIC/群集 |

### <a name="helm-deployed-agic-single-aks-cluster"></a>Helm 部署 AGIC （单一 AKS 群集）
|                  |1个应用程序网关 |2 + 应用程序网关 |
|------------------|---------|--------|
|**1 AGIC**|是的，支持 |不是，这是我们的积压工作 |
|**2 + AGICs**|必须使用共享的 ProhibitedTarget 功能，并监视单独的命名空间 |是的，支持 |

### <a name="helm-deployed-agic-2-aks-clusters"></a>Helm 部署 AGIC （2 + AKS 群集）
|                  |1个应用程序网关 |2 + 应用程序网关 |
|------------------|---------|--------|
|**1 AGIC**|N/A |不适用 |
|**2 + AGICs**|必须使用共享的 ProhibitedTarget 功能 |不适用 |

## <a name="next-steps"></a>后续步骤
- [**AKS 外接程序领域部署**](tutorial-ingress-controller-add-on-new.md)：有关在空白-石板基础结构上安装 AGIC 外接程序、AKS 和应用程序网关的说明。
- [**AKS 外接程序要重建 mqtt 部署**](tutorial-ingress-controller-add-on-existing.md)：使用现有应用程序网关在 AKS 群集上安装 AGIC 外接程序。
- [**Helm 领域部署**](ingress-controller-install-new.md)：在的基础结构上安装 AGIC 到 Helm、新的 AKS 群集和新的应用程序网关。
- [**Helm 要重建 mqtt Deployment**](ingress-controller-install-existing.md)：通过 Helm 在现有的 AKS 群集和应用程序网关上部署 AGIC。


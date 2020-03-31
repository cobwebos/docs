---
title: 什么是 Azure 应用程序网关入口控制器？
description: 本文简单介绍了什么是应用程序网关入口控制器。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: d6a63b6276c07b1fe6487b97f5c7fc255b6d3411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335799"
---
# <a name="what-is-application-gateway-ingress-controller"></a>什么是应用程序网关入口控制器？
应用程序网关入口控制器 (AGIC) 是一个 Kubernetes 应用程序。有了它，[Azure Kubernetes 服务 (AKS)](https://azure.microsoft.com/services/kubernetes-service/) 客户就可以利用 Azure 的本机[应用程序网关](https://azure.microsoft.com/services/application-gateway/) L7 负载均衡器向 Internet 公开云软件。 AGIC 监视托管时所在的 Kubernetes 群集并持续更新应用程序网关，以便向 Internet 公开所选服务。

在客户的 AKS 上，入口控制器在其自己的 Pod 中运行。 AGIC 监视部分 Kubernetes 资源中的更改。 AKS 群集的状态会转换为特定于应用程序网关的配置并应用到 [Azure 资源管理器 (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。

## <a name="benefits-of-application-gateway-ingress-controller"></a>应用程序网关入口控制器的好处
有了 AGIC，部署就可以通过单个应用程序网关入口控制器来控制多个 AKS 群集。 另外，有了 AGIC，就不需在 AKS 群集前面设置另一个负载均衡器/公共 IP，避免在请求到达 AKS 群集之前在数据路径中设置多个跃点。 应用程序网关直接使用其专用 IP 与 Pod 通信，不需要 NodePort 或 KubeProxy 服务。 这也会改进部署性能。

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

AGIC 能够处理多个命名空间并有 ProhibitedTargets，这意味着 AGIC 可以专为 AKS 群集配置应用程序网关，不影响其他现有的后端。 

## <a name="next-steps"></a>后续步骤

- [**绿地部署**](ingress-controller-install-new.md)：有关在空白板式基础结构上安装 AGIC、AKS 和应用程序网关的说明。
- [**布朗菲尔德部署**](ingress-controller-install-existing.md)：在现有 AKS 和应用程序网关上安装 AGIC。


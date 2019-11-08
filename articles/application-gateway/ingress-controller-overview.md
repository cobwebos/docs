---
title: 什么是 Azure 应用程序网关入口控制器？
description: 本文介绍了应用程序网关入口控制器的简介。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 9635798720667e38a767f26fc2e5f5374e420059
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795537"
---
# <a name="what-is-application-gateway-ingress-controller"></a>什么是应用程序网关入口控制器？
应用程序网关入口控制器（AGIC）是一个 Kubernetes 应用程序，使[Azure Kubernetes 服务（AKS）](https://azure.microsoft.com/services/kubernetes-service/)客户能够利用 azure 的本机[应用程序网关](https://azure.microsoft.com/services/application-gateway/)L7 负载平衡器向Internet。 AGIC 监视它所托管的 Kubernetes 群集并不断更新应用程序网关，以便将所选服务公开到 Internet。

入口控制器在客户的 AKS 上的自己的 pod 中运行。 AGIC 监视 Kubernetes 资源的子集以进行更改。 AKS 群集的状态将转换为特定于应用程序网关的配置，并应用于[Azure 资源管理器（ARM）](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。

## <a name="benefits-of-application-gateway-ingress-controller"></a>应用程序网关入口控制器的优点
AGIC 允许部署使用单个应用程序网关入口控制器控制多个 AKS 群集。 AGIC 还有助于消除在 AKS 群集之前有另一个负载平衡器/公共 IP 的需要，并在请求到达 AKS 群集之前避免数据路径中存在多个跃点。 应用程序网关直接使用其专用 IP 与 pod 通信，不需要 NodePort 或 KubeProxy 服务。 这还可以提高部署的性能。

入口控制器仅由 Standard_v2 和 WAF_v2 Sku 支持，这也会使你能够自动缩放。 应用程序网关可以做出反应，以响应流量负载增加或减少，并相应地进行调整，而无需使用 AKS 群集中的任何资源。

除了 AGIC 之外，使用应用程序网关还可通过提供 TLS 策略和 Web 应用程序防火墙（WAF）功能来帮助保护你的 AKS 群集。

![Azure 应用程序网关 + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC 是通过 Kubernetes[入口资源](http://kubernetes.io/docs/user-guide/ingress/)配置的，以及服务和部署/箱。 它提供多种功能，利用 Azure 的本机应用程序网关 L7 负载均衡器。 要命名几个：
  - URL 路由
  - 基于 Cookie 的相关性
  - SSL 终止
  - 端到端 SSL
  - 对公共网站、私有网站和混合网站的支持
  - 集成式 Web 应用程序防火墙

AGIC 可以处理多个命名空间并具有 ProhibitedTargets，这意味着 AGIC 可以专门为 AKS 群集配置应用程序网关，而不会影响其他现有的后端。 

## <a name="next-steps"></a>后续步骤

- [**领域部署**](ingress-controller-install-new.md)：有关在空石板基础结构上安装 AGIC、AKS 和应用程序网关的说明。
- [**要重建 mqtt 部署**](ingress-controller-install-existing.md)：在现有 AKS 和应用程序网关上安装 AGIC。


---
title: Azure 资源管理器模板示例 - Azure Front Door 服务 | Microsoft Docs
description: 适用于 Azure Front Door 服务的 Azure 资源管理器模板示例
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2018
ms.author: sharadag
ms.openlocfilehash: 1e4571edb15bf1a06948e720e51c672f890d68b6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959507"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>适用于 Front Door 的 Azure 资源管理器部署模型模板

下表包含适用于 Azure Front Door 服务的 Azure 资源管理器部署模型模板的链接。 

| | |
| ---| ---|
| [创建基本 Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic)| 通过一个后端创建基本 Front Door 配置。 |
| [Create a Front Door with multiple backends and backend pools and URL based routing](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends)（通过多个后端和后端池以及基于 URL 的路由创建 Front Door）| 基于 URL 路径，使用 ta 后端池中以及跨多个后端池为多个后端配置的负载均衡创建 Front Door。 |
| [Onboard a custom domain with HTTPS (Front Door managed cert) with Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)（通过 Front Door 载入带有 HTTPS [Front Door 托管证书] 的自定义域）| 将自定义域添加到 Front Door，并通过 DigiCert 生成的 Front Door 托管证书为其启用 HTTPS 流量。 |
| [Create Front Door with geo filtering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)（使用地区筛选创建 Front Door）| 创建允许或阻止来自某些国家/地区的流量的 Front Door。 |
| [Control Health Probes for your backends on Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes)（在 Front Door 上控制后端的健康状况探测）| 通过更新探测路径以及探测发送的时间间隔来更新 Front Door，从而更改健康状况探测设置。 |
| [Create Front Door with Active/Standby backend configuration](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb)（通过活动/备用后端配置创建 Front Door）| 创建一个 Front Door，它演示活动/备用应用程序拓扑基于优先级的路由，即默认情况下将所有流量发送到主（最高优先级）后端，直到其不再可用。 |
| [Create Front Door with caching enabled for certain routes](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching)（通过为某些路由启用的缓存创建 Front Door）| 使用为定义的路由配置启用的缓存创建一个 Front Door，从而为工作负载缓存任何静态资产。 |
| [Configure Session Affinity for your Front Door host names](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity)（为 Front Door 主机名配置会话相关性） | 更新 Front Door 以启用前端主机的会话相关性，从而将后续流量从同一用户会话发送到同一后端。 |
| [Onboard a custom domain with custom SSL certificate (for HTTPS) for your Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-custom-byoc)（通过自定义 SSL 证书（用于 HTTPS）载入 Front Door 的自定义域） | 通过自定义 SSL 证书载入 Front Door 的自定义域（也称为“自带证书”方案）。 |
| [Configure Front Door for client IP whitelisting or blacklisting](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)（为客户端 IP 白名单或黑名单的设置配置 Front Door）| 配置 Front Door 以使用客户端 IP 通过自定义访问控制来限制某些客户端 IP 的流量。 |
| [Configure Front Door to take action with specific http parameters](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)（配置 Front Door 以对特定的 http 参数采取措施）| 使用 http 参数通过访问控制的自定义规则，根据传入请求中的 http 参数配置 Front Door 是允许还是阻止某些流量。 |
| [Configure Front Door rate limiting](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)（配置 Front Door 速率限制）| 配置 Front Door 对给定前端主机的传入流量进行速率限制。 |
| | |

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
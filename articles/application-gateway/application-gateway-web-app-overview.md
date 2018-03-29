---
title: 带 Azure 应用程序网关的多租户后端概述 | Microsoft Docs
description: 此页概述了应用程序网关对多租户后端的支持。
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: ''
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: f5ec916fcf45734ab85751e749bd6bb312f05b1a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="application-gateway-support-for-multi-tenant-back-ends"></a>应用程序网关对多租户后端的支持

Azure 应用程序网关支持将虚拟机规模集、网络接口、公共/专用 IP 或完全限定的域名 (FQDN) 用作其后端池的一部分。 默认情况下，应用程序网关不更改从客户端传入的 HTTP 主机标头，而是将该标头原封不动地发送到后端。 有许多服务（例如 [Azure Web 应用](../app-service/app-service-web-overview.md)）从性质上来说是多租户的，依赖于特定的主机标头或 SNI 扩展，否则无法解析为正确的终结点。 应用程序网关现在允许用户根据后端 HTTP 设置覆盖传入的 HTTP 主机标头。 此功能支持多租户后端 Azure Web 应用和 API 管理。 此功能适用于标准 SKU 和 WAF SKU。 多租户后端支持也适用于 SSL 终止和端到端 SSL 方案。

![Web 应用方案](./media/application-gateway-web-app-overview/scenario.png)

指定主机替代这一功能在 HTTP 设置中定义，可以在创建规则过程中应用于任何后端池。 多租户后端支持通过以下两种方式来替代主机标头和 SNI 扩展。

1. 在 HTTP 设置中将主机名设置为固定值的功能。 此功能可确保将主机标头替代为该值，前提是在流量流向的后端池中应用了 HTTP 设置。 使用端到端 SSL 时，会在 SNI 扩展中使用该替代的主机名。 有了此功能，后端池场收到的主机标头就可以不同于传入的客户主机标头。

2. 从后端池成员的 IP 或 FQDN 派生主机名的功能。 HTTP 设置也提供了一个选项，允许从后端池成员的 FQDN 选取主机名，前提是配置了从单个后端池成员派生主机名的选项。 使用端到端 SSL 时，该主机名派生自 FQDN，用在 SNI 扩展中。 有了此功能，后端池就可以有两个或两个以上的多租户 PaaS 服务（例如 Azure Web 应用），而针对每个成员的请求的主机标头就可以包含从该成员的 FQDN 派生的主机名。

> [!NOTE]
> 在前面的两个示例中，设置只影响实时流量行为，不影响运行状况探测行为。 自定义探测已经支持在探测配置中指定主机标头的功能。 自定义探测现在还支持从当前配置的 HTTP 设置派生主机标头行为的功能。 指定此配置时，可以在探测配置中使用 `PickHostNameFromBackendHttpSettings` 参数。 若要使用端到端功能，必须修改探测和 HTTP 设置，使之反映正确的配置。

有了此功能，客户就可以在 HTTP 设置中指定选项，并根据相应的配置来自定义探测。 然后，可以通过规则将此设置绑定到侦听器和后端池。

## <a name="next-steps"></a>后续步骤

若要了解如何设置应用程序网关，以便将 Web 应用用作后端池成员，请访问：[为应用服务 Web 应用配置应用程序网关](application-gateway-web-app-powershell.md)

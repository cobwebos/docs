---
title: Azure Web 应用程序防火墙简介
description: 本文提供 Azure Web 应用程序防火墙 (WAF) 的概述
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "79475781"
---
# <a name="what-is-azure-web-application-firewall"></a>什么是 Azure Web 应用程序防火墙？

Web 应用程序防火墙 (WAF) 为 Web 应用程序提供集中保护，使其免受常见攻击和漏洞的侵害。 Web 应用程序已逐渐成为利用常见已知漏洞的恶意攻击的目标。 SQL 注入和跨站点脚本是最常见的攻击。

![WAF 概述](media/overview/wafoverview.png)

在应用程序代码中防止此类攻击非常困难。 这可能需要在应用程序拓扑的多个层进行严格的维护、修补和监视。 集中式 Web 应用程序防火墙有助于大幅简化安全性管理。 WAF 还能使应用程序管理员更有把握地防范威胁和入侵。

相较保护每个单独的 Web 应用程序，WAF 解决方案可以通过在中心位置修补已知漏洞，更快地对安全威胁做出反应。

## <a name="supported-service"></a>支持的服务

WAF 可以通过 Microsoft 的 Azure 应用程序网关、Azure Front Door 和 Azure 内容分发网络 (CDN) 服务进行部署。 Azure CDN 上的 WAF 目前为公共预览版。  WAF 提供针对每个特定服务自定义的功能。 有关适用于每个服务的 WAF 功能的详细信息，请参阅每个服务的概述。

## <a name="next-steps"></a>后续步骤

- 有关应用程序网关上的 Web 应用程序防火墙的详细信息，请参阅 [Azure 应用程序网关上的 Web 应用程序防火墙](./ag/ag-overview.md)。
- 有关 Azure Front Door 服务上的 Web 应用程序防火墙的详细信息，请参阅 [Azure Front Door 服务上的 Web 应用程序防火墙](./afds/afds-overview.md)。
- 有关 Azure CDN 服务上的 Web 应用程序防火墙的详细信息，请参阅 [Azure CDN 服务上的 Web 应用程序防火墙](./cdn/cdn-overview.md)。

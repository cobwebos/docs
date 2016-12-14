---
title: "配合使用 Azure DNS 和其他 Azure 服务 | Microsoft Docs"
description: "了解如何使用 Azure DNS 来解析其他 Azure 服务的名称"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 42d47741e414b2de177f1fd75b3e1ac3fde96579
ms.openlocfilehash: 15412e67b6785812d00438e1819f24a6cb4fd0a8

---
# <a name="using-azure-dns-with-other-azure-services"></a>配合使用 Azure DNS 和其他 Azure 服务

Azure DNS 是一种托管的 DNS 管理和名称解析服务。 这样，你能够为已在 Azure 中部署的其他应用程序和服务创建公共 DNS 名称。 为你的自定义域中为 Azure 服务创建名称，与为服务添加正确类型的记录一样简单。

* 对于动态分配的 IP 地址，必须创建 DNS CNAME 记录，用于映射到 Azure 为你的服务创建的 DNS 名称。 DNS 标准阻止你使用区域顶点的 CNAME 记录。
* 对于静态分配的 IP 地址，可以使用任何名称创建一条 DNS A 记录，包括在区域顶点处的*裸域*名称。

下表概述了可用于各种 Azure 服务的受支持的记录类型。 从此表中可以看出，Azure DNS 仅支持面向 Internet 的网络资源的 DNS 记录。 Azure DNS 不能用于内部专用地址的名称解析。

| Azure 服务 | 网络接口 | 说明 |
| --- | --- | --- |
| 应用程序网关 |前端的公共 IP |可以创建 DNS A 或 CNAME 记录。 |
| 负载平衡器 |前端的公共 IP |可以创建 DNS A 或 CNAME 记录。 负载平衡器可以拥有一个动态分配的 IPv6 公共 IP 地址。 因此，你必须为 IPv6 地址创建 CNAME 记录。 |
| 流量管理器 |公共名称 |只能创建映射到分配给你的流量管理器配置文件的 trafficmanager.net 名称的 CNAME。 有关详细信息，请参阅[流量管理器工作原理](../traffic-manager/traffic-manager-how-traffic-manager-works.md#traffic-manager-example)。 |
| 云服务 |公共 IP |对于静态分配的 IP 地址，可以创建一条 DNS A 记录。 对于动态分配的 IP 地址，必须创建一条映射到 *cloudapp.net* 名称的 CNAME 记录。 此规则适用于在经典门户中创建的 VM，因为它们是作为云服务部署的。 有关详细信息，请参阅[在云服务中配置自定义域名](../cloud-services/cloud-services-custom-domain-name-portal.md)。 |
| App Service |外部 IP |对于外部 IP 地址，可以创建一条 DNS A 记录。 否则，必须创建一条映射到 azurewebsites.net 名称的 CNAME 记录。 有关详细信息，请参阅[将自定义域名映射到 Azure 应用](../app-service-web/web-sites-custom-domain-name.md) |
| Resource Manager VM |公共 IP |Resource Manager VM 可以具有公共 IP 地址。 使用公共 IP 地址的 VM 也可能在负载平衡器后面。 你可以为公共地址创建 DNS A 或 CNAME 记录。 此自定义名称可用于绕过负载平衡器中的 VIP。 |
| 经典 VM |公共 IP |使用 PowerShell 或 CLI 创建的经典 VM 可以配置为使用动态或静态（保留）的虚拟地址。 你可以分别创建 DNS CNAME 或 A 记录。 |




<!--HONumber=Nov16_HO3-->



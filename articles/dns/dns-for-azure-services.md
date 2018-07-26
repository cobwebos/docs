---
title: 配合使用 Azure DNS 和其他 Azure 服务 | Microsoft Docs
description: 了解如何使用 Azure DNS 来解析其他 Azure 服务的名称
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: victorh
ms.openlocfilehash: 2f5ff425eadc4572f5e109f503c57969ab310f6b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171800"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Azure DNS 如何与其他 Azure 服务一起工作

Azure DNS 是一种托管的 DNS 管理和名称解析服务。 这样，能够为已在 Azure 中部署的其他应用程序和服务创建公共 DNS 名称。 为自定义域中为 Azure 服务创建名称，与为服务添加正确类型的记录一样简单。

* 对于动态分配的 IP 地址，必须创建 DNS CNAME 记录，用于映射到 Azure 为服务创建的 DNS 名称。 DNS 标准将阻止你使用区域顶点的 CNAME 记录。
* 对于静态分配的 IP 地址，可以使用任何名称创建一条 DNS A 记录，包括在区域顶点处的*裸域*名称。

下表概述了可用于各种 Azure 服务的受支持的记录类型。 从此表中可以看出，Azure DNS 仅支持面向 Internet 的网络资源的 DNS 记录。 Azure DNS 不能用于内部专用地址的名称解析。

| Azure 服务 | 网络接口 | Description |
| --- | --- | --- |
| 应用程序网关 |[前端的公共 IP](dns-custom-domain.md#public-ip-address) |可以创建 DNS A 或 CNAME 记录。 |
| 负载均衡器 |[前端的公共 IP](dns-custom-domain.md#public-ip-address)  |可以创建 DNS A 或 CNAME 记录。 负载均衡器可以拥有一个动态分配的 IPv6 公共 IP 地址。 因此，必须为 IPv6 地址创建 CNAME 记录。 |
| 流量管理器 |公共名称 |只能创建映射到分配给流量管理器配置文件的 trafficmanager.net 名称的 CNAME。 有关详细信息，请参阅[流量管理器工作原理](../traffic-manager/traffic-manager-overview.md#traffic-manager-example)。 |
| 云服务 |[公共 IP](dns-custom-domain.md#public-ip-address) |对于静态分配的 IP 地址，可以创建一条 DNS A 记录。 对于动态分配的 IP 地址，必须创建一条映射到 *cloudapp.net* 名称的 CNAME 记录。|
| 应用服务 | [外部 IP](dns-custom-domain.md#app-service-web-apps) |对于外部 IP 地址，可以创建一条 DNS A 记录。 否则，必须创建一条映射到 azurewebsites.net 名称的 CNAME 记录。 有关详细信息，请参阅[将自定义域名映射到 Azure 应用](../app-service/app-service-web-tutorial-custom-domain.md) |
| Resource Manager VM |[公共 IP](dns-custom-domain.md#public-ip-address) |Resource Manager VM 可以具有公共 IP 地址。 使用公共 IP 地址的 VM 也可能在负载均衡器后面。 可以为公共地址创建 DNS A 或 CNAME 记录。 此自定义名称可用于绕过负载均衡器中的 VIP。 |
| 经典 VM |[公共 IP](dns-custom-domain.md#public-ip-address) |使用 PowerShell 或 CLI 创建的经典 VM 可以配置为使用动态或静态（保留）的虚拟地址。 可以分别创建 DNS CNAME 或 A 记录。 |

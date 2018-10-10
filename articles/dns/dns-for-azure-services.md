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
ms.openlocfilehash: 5d9886e16aa1921963f3d91d0fbd4da9287f7e54
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989135"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Azure DNS 如何与其他 Azure 服务一起工作

Azure DNS 是一种托管的 DNS 管理和名称解析服务。 这样，能够为已在 Azure 中部署的其他应用程序和服务创建公共 DNS 名称。 为自定义域中为 Azure 服务创建名称，与为服务添加正确类型的记录一样简单。

* 对于动态分配的 IP 地址，可以创建一条 DNS CNAME 记录，该记录映射到 Azure 为服务创建的 DNS 名称。 DNS 标准将阻止你使用区域顶点的 CNAME 记录，但可改为使用别名记录。 有关详细信息，请参阅[教程：配置表示 Azure 公共 IP 地址的别名记录](tutorial-alias-pip.md)。
* 对于静态分配的 IP 地址，可以使用任何名称创建一条 DNS A 记录，包括在区域顶点处的*裸域*名称。

下表概述了可用于各种 Azure 服务的受支持的记录类型。 从此表中可以看出，Azure DNS 仅支持面向 Internet 的网络资源的 DNS 记录。 Azure DNS 不能用于内部专用地址的名称解析。

| Azure 服务 | 网络接口 | Description |
| --- | --- | --- |
| 应用程序网关 |[前端的公共 IP](dns-custom-domain.md#public-ip-address) |可以创建 DNS A 或 CNAME 记录。 |
| 负载均衡器 |[前端的公共 IP](dns-custom-domain.md#public-ip-address)  |可以创建 DNS A 或 CNAME 记录。 负载均衡器可以拥有一个动态分配的 IPv6 公共 IP 地址。 因此，必须为 IPv6 地址创建 CNAME 记录。 |
| 流量管理器 |公共名称 |可创建映射到分配给流量管理器配置文件的 trafficmanager.net 名称的别名记录。 有关详细信息，请参阅[教程：配置使用流量管理器支持顶点域名的别名记录](tutorial-alias-tm.md)。 |
| 云服务 |[公共 IP](dns-custom-domain.md#public-ip-address) |对于静态分配的 IP 地址，可以创建一条 DNS A 记录。 对于动态分配的 IP 地址，必须创建一条映射到 *cloudapp.net* 名称的 CNAME 记录。|
| 应用服务 | [外部 IP](dns-custom-domain.md#app-service-web-apps) |对于外部 IP 地址，可以创建一条 DNS A 记录。 否则，必须创建一条映射到 azurewebsites.net 名称的 CNAME 记录。 有关详细信息，请参阅[将自定义域名映射到 Azure 应用](../app-service/app-service-web-tutorial-custom-domain.md) |
| Resource Manager VM |[公共 IP](dns-custom-domain.md#public-ip-address) |Resource Manager VM 可以具有公共 IP 地址。 使用公共 IP 地址的 VM 也可能在负载均衡器后面。 可以为公共地址创建 DNS A、CNAME 或别名记录。 此自定义名称可用于绕过负载均衡器中的 VIP。 |
| 经典 VM |[公共 IP](dns-custom-domain.md#public-ip-address) |使用 PowerShell 或 CLI 创建的经典 VM 可以配置为使用动态或静态（保留）的虚拟地址。 可以分别创建 DNS CNAME 或 A 记录。 |

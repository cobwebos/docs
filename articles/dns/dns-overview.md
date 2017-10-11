---
title: "Azure DNS 概述 | Microsoft Docs"
description: "Microsoft Azure 上的 DNS 托管服务概述。 在 Microsoft Azure 上托管域。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 68747a0d-b358-4b8e-b5e2-e2570745ec3f
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: 3705457e4c90f8869496f7f5177531bd128d1057
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="azure-dns-overview"></a>Azure DNS 概述

域名系统或 DNS 负责将网站或服务名称转换（或解析）为它的 IP 地址。 Azure DNS 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。

![DNS 概述](./media/dns-overview/scenario.png)

## <a name="features"></a>功能

* 可靠性和性能 - Azure DNS 中的 DNS 域托管在 DNS 名称服务器的 Azure 全局网络上。 我们使用任意广播网络，以便每个 DNS 查询由最近的可用 DNS 服务器来应答。 这为域提供更快的性能和高可用性。

* 无缝集成 - Azure DNS 服务可用于管理 Azure 服务的 DNS 记录，还可用于为外部资源提供 DNS。 Azure DNS 集成在 Azure 门户中，与其他 Azure 服务使用相同的凭据、计费和支持协定。

* 安全性 - Azure DNS 服务基于 Azure Resource Manager。 因此，它可以利用 Resource Manager 功能，例如基于角色的访问控制、审核日志和资源锁定。 可以通过 Azure 门户、Azure PowerShell cmdlet 和跨平台 Azure CLI 对域和记录进行管理。 需要自动 DNS 管理的应用程序可通过 REST API 和 SDK 与服务集成。

Azure DNS 当前不支持购买域名。 如果想要购买域，则需要使用第三方域名注册机构。 注册机构通常收取小额年费。 域然后可以在 Azure DNS 中的 DNS 记录管理的托管。 有关详细信息，请参阅[向 Azure DNS 委托域](dns-domain-delegation.md)。

## <a name="pricing"></a>定价

DNS 基于 Azure 中托管的 DNS 区域数并按 DNS 查询数进行计费。 若要深入了解定价，请访问 [Azure DNS 定价](https://azure.microsoft.com/pricing/details/dns/)。

## <a name="faq"></a>常见问题

有关 Azure DNS 的常见问题，请参阅 [Azure DNS 常见问题](dns-faq.md)。

## <a name="next-steps"></a>后续步骤

若要了解 DNS 区域和记录，请访问 [DNS 区域和记录概述](dns-zones-records.md)。

了解如何在 Azure DNS 中[创建 DNS 区域](./dns-getstarted-create-dnszone-portal.md)。

了解 Azure 的部分其他关键[网络功能](../networking/networking-overview.md)。


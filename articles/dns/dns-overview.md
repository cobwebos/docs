---
title: "Azure DNS 概述 | Microsoft Docs"
description: "在 Microsoft Azure 上托管服务的 Azure DNS 的概述。 在 Microsoft Azure 上托管你的域。"
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
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 42d47741e414b2de177f1fd75b3e1ac3fde96579
ms.openlocfilehash: 32b6c12a07f67d39d7a1dcf213f0372d3c3cd40c

---

# <a name="azure-dns-overview"></a>Azure DNS 概述

域名系统或 DNS 负责将网站或服务名称转换（或解析）为它的 IP 地址。 Azure DNS 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管你的域，你可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理你的 DNS 记录。

Azure DNS 中的 DNS 域托管在 DNS 名称服务器的 Azure 全球网络上。 我们使用任意广播网络，以便每个 DNS 查询由最近的可用 DNS 服务器来应答。 这为你的域提供更快的性能和高可用性。

Azure DNS 服务基于 Azure Resource Manager (ARM)。 因此，它可以利用 ARM 功能，例如基于角色的访问控制、审核日志和资源锁定。 可以通过 Azure 门户、Azure PowerShell cmdlet 和跨平台 Azure CLI 对域和记录进行管理。 需要自动 DNS 管理的应用程序可通过 REST API 和 SDK 与服务集成。

Azure DNS 当前不支持购买域名。 如果想要购买域，则需要使用第三方域名注册机构。 注册机构通常将收取小额年费。 然后，域可以托管在 Azure DNS 中以管理 DNS 记录。 有关详细信息，请参阅[向 Azure DNS 委托域](dns-domain-delegation.md)。

## <a name="next-steps"></a>后续步骤

[创建 DNS 区域](dns-getstarted-create-dnszone-portal.md)




<!--HONumber=Nov16_HO3-->



---
title: "将 Azure DNS 用于专用域 | Microsoft Docs"
description: "Microsoft Azure 上的专用 DNS 托管服务概述。"
services: dns
documentationcenter: na
author: garbrad
manager: 
editor: 
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/04/2017
ms.author: garbrad
ms.openlocfilehash: ed47a9d850995aaf9e78bfde8b6a5fd80dc36918
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="using-azure-dns-for-private-domains"></a>将 Azure DNS 用于专用域
域名系统（或称为 DNS）负责将服务名称转换（或解析）为其 IP 地址。 Azure DNS 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。  除了面向 Internet 的 DNS 域之外，作为一项预览版功能，Azure DNS 现在还支持专用 DNS 域。  
 
Azure DNS 提供了可靠、安全的 DNS 服务来管理和解析虚拟网络中的域名，无需添加自定义 DNS 解决方案。 专用 DNS 区域允许使用自定义域名而不使用当前可用的由 Azure 提供的名称。  使用自定义域名可帮助你定制虚拟网络体系结构以便最好地满足组织需求。 它在虚拟网络内以及在虚拟网络之间针对 VM 提供名称解析。 除此之外，还可以通过水平分割视图配置区域名称，从而允许专用和公用 DNS 区域共享相同的名称。

![DNS 概述](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="benefits"></a>优点

* **无需使用自定义 DNS 解决方案。** 以前，许多客户创建了自定义 DNS 解决方案来管理其虚拟网络。  现在可以使用 Azure 的本机基础结构执行 DNS 区域管理，这解除了创建和管理自定义 DNS 解决方案的负担。

* **使用所有常见的 DNS 记录类型。**  Azure DNS 支持 A、AAAA、CNAME、MX、NS、PTR、SOA、SRV 和 TXT 记录。

* **自动化主机名记录管理。** 除了承载自定义 DNS 记录之外，Azure 还会自动维护指定虚拟网络中的 VM 的主机名记录。  这使得你可以优化所使用的域名，不需要创建自定义 DNS 解决方案或修改应用程序。

* **虚拟网络之间的主机名解析。** 不同于 Azure 提供的主机名，专用 DNS 区域可以在虚拟网络之间共享。  此功能简化了跨网络和服务发现方案，例如，虚拟网络对等互连。

* **熟悉的工具和用户体验。** 为了降低学习难度，此新的产品/服务使用已经成熟的 Azure DNS 工具（PowerShell、资源管理器模板、REST API）并且会尽快支持使用 CLI 和门户。

* **水平分割 DNS 支持。** Azure DNS 允许使用相同的名称创建在虚拟网络内与在公共 Internet 内分别解析为不同结果的区域。  水平分割 DNS 的典型方案是提供一个专用服务版本以在虚拟网络内部使用。


## <a name="pricing"></a>定价

在托管预览版期间，专用 DNS 区域将免费。 在公开上市时，此功能将使用基于使用量的定价模型，这与现有的 Azure DNS 产品/服务类似。 


## <a name="next-steps"></a>后续步骤

了解如何在 Azure DNS 中[创建专用 DNS 区域](./private-dns-getstarted-powershell.md)。

若要了解 DNS 区域和记录，请访问 [DNS 区域和记录概述](dns-zones-records.md)。

了解 Azure 的部分其他关键[网络功能](../networking/networking-overview.md)。


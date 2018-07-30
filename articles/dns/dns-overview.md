---
title: 什么是 Azure DNS？
description: Microsoft Azure 上的 DNS 托管服务概述。 在 Microsoft Azure 上托管域。
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: overview
ms.date: 6/7/2018
ms.author: victorh
ms.openlocfilehash: e95617664ee30f1b9253f1892176fd39649ee2c2
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39174626"
---
# <a name="what-is-azure-dns"></a>什么是 Azure DNS？

Azure DNS 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。

不能使用 Azure DNS 来购买域名。 对于年度费用，可以使用 [Azure Web 应用](https://docs.microsoft.com/en-us/azure/app-service/custom-dns-web-site-buydomains-web-app#buy-the-domain)或第三方域名注册机构购买域名。 然后，可以将域托管在 Azure DNS 中来管理记录。 有关详细信息，请参阅[向 Azure DNS 委托域](dns-domain-delegation.md)。

Azure DNS 附带了以下功能：

## <a name="reliability-and-performance"></a>可靠性和性能

Azure DNS 中的 DNS 域托管在 DNS 名称服务器的 Azure 全球网络上。 Azure DNS 使用任意广播网络，以便每个 DNS 查询由最近的可用 DNS 服务器来应答。 这为域提供更快的性能和高可用性。

## <a name="security"></a>安全性

Azure DNS 服务基于 Azure 资源管理器。 因此，你会获得下列资源管理器功能：

* [基于角色的访问控制](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#access-control) - 控制谁有权访问针对组织的特定操作。

* [活动日志](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#activity-logs) - 监视你的组织中的用户对资源进行了怎样的修改，或者在进行故障排除时查找错误。

* [资源锁定](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-lock-resources) - 锁定订阅、资源组或资源，以防止组织中的其他用户意外删除或修改关键资源。

有关详细信息，请参阅[如何保护 DNS 区域和记录](dns-protect-zones-recordsets.md)。 


## <a name="ease-of-use"></a>易于使用

Azure DNS 服务可以管理 Azure 服务的 DNS 记录，还可以为外部资源提供 DNS。 Azure DNS 集成在 Azure 门户中，与其他 Azure 服务使用相同的凭据、支持合同和计费功能。 

DNS 基于 Azure 中托管的 DNS 区域数并按 DNS 查询数进行计费。 若要深入了解定价，请参阅 [Azure DNS 定价](https://azure.microsoft.com/pricing/details/dns/)。

可以通过 Azure 门户、Azure PowerShell cmdlet 和跨平台 Azure CLI 对域和记录进行管理。 需要自动 DNS 管理的应用程序可通过 REST API 和 SDK 与服务进行集成。

## <a name="customizable-virtual-networks-with-private-domains"></a>具有专用域的可自定义虚拟网络

Azure DNS 还支持专用 DNS 区域，此功能当前为公共预览版。 这允许在专用虚拟网络中使用自定义域名而不使用当前可用的由 Azure 提供的名称。

有关详细信息，请参阅[在专用域中使用 Azure DNS](private-dns-overview.md)。


## <a name="next-steps"></a>后续步骤

* 了解 DNS 区域和记录：[DNS 区域和记录概述](dns-zones-records.md)。

* 了解如何在 Azure DNS 中创建区域：[创建 DNS 区域](./dns-getstarted-create-dnszone-portal.md)。

* 有关 Azure DNS 的常见问题，请参阅 [Azure DNS 常见问题](dns-faq.md)。


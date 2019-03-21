---
title: 为使用流量管理器的应用配置 DNS 名称 - Azure 应用服务
description: 为 Azure 应用服务中包含流量管理器（用于负载均衡）的 Web 应用使用自定义域名。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b207d9e3123245e5cb7dddb625f7488886591ae5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089856"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>使用流量管理器为 Azure 应用服务中的 Web 应用配置自定义域名
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文提供了将自定义域名与一个[应用服务](overview.md)应用配合使用的一般说明，该应用已与[流量管理器](../traffic-manager/traffic-manager-overview.md)集成以进行负载均衡。

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>了解 DNS 记录
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>将 Web 应用配置为标准模式
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>为自定义域添加 DNS 记录
> [!NOTE]
> 如果已通过 Azure 应用服务 Web 应用购买了域，请跳过下面的步骤并参阅[为 Web 应用购买域](manage-custom-dns-buy-domain.md)文章的最后一个步骤。
> 
> 

要将自定义域与 Azure 应用服务中的 Web 应用关联，必须在 DNS 表中为自定义域添加新条目。 请通过使用来自域提供商的管理工具执行此操作。

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

虽然各个域提供商的具体情况不同，但你都需从自定义域名（例如 **contoso.com**）映射到与 Web 应用集成的流量管理器域名 (**contoso.trafficmanager.net**)。

> [!NOTE]
> 如果某条记录已被使用并且你需要先将你的应用绑定到该记录，可以创建一条额外的 CNAME 记录。 例如，若要提前将绑定**www\.contoso.com**到 web 应用，创建从一条 CNAME 记录**awverify.www**到**contoso.trafficmanager.net**。 然后，可以添加"www\.contoso.com"到 Web 应用而无需更改"www"CNAME 记录。 有关详细信息，请参阅[在自定义域中为 Web 应用创建 DNS 记录][CREATEDNS]。

在域提供商处添加或修改完 DNS 记录后，请保存这些更改。

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>启用流量管理器
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅 [Node.js 开发人员中心](https://azure.microsoft.com/develop/nodejs/)。

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md

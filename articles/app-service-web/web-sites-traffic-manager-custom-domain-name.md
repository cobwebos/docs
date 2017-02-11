---
title: "为 Azure App Service 中使用流量管理器进行负载平衡的 Web 应用配置自定义域名。"
description: "为 Azure App Service 中包含流量管理器（用于负载平衡）的 Web 应用使用自定义域名。"
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: wpickett
editor: 
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0f8d2941b30079463954b4f9b8b9dfb10cfa3ea1


---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>使用流量管理器为 Azure App Service 中的 Web 应用配置自定义域名
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文提供了将自定义域名与通过流量管理器进行负载平衡的 Azure App Service 配合使用的一般说明。

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
> 如果已通过 Azure 应用服务 Web 应用购买了域，请跳过下面的步骤并参阅[为 Web 应用购买域](custom-dns-web-site-buydomains-web-app.md)文章的最后一个步骤。
> 
> 

若要将自定义域与 Azure App Service 中的 Web 应用关联，必须通过使用向你出售域名的域注册机构所提供的工具在 DNS 表中为自定义域添加新条目。 使用以下步骤找到并使用这些 DNS 工具。

1. 登录到在域注册机构注册的帐户，然后查找用于管理 DNS 记录的页面。 查找网站中标签为“域名”、“DNS”或“名称服务器管理”的链接或区域。 通过查看帐户信息，然后查找诸如“我的域”这样的链接，通常可以找到此页面的链接。
2. 找到域名管理页后，再找允许编辑 DNS 记录的链接。 此链接可能作为“区域文件”、“DNS 记录”或“高级”配置链接列出。
   
   * 该页面将很可能有几个已经创建的记录，例如将 '**@**' 或 '\*' 与“域驻留”页面关联的条目。 它还可能包含用于常见子域（例如 **www**）的记录。
   * 该页将涉及 **CNAME 记录**，或者提供用于选择记录类型的下拉列表。 它还可能涉及其他记录，例如 **A 记录**和 **MX 记录**。 在某些情况下，CNAME 记录将通过其他名称（例如**别名记录**）调用。
   * 该页还提供了用于从 **主机名** 或 **域名** **映射** 到另一域名的字段。
3. 虽然各个注册机构的具体情况不同，但是一般而言，是 *从* 自定义域名（例如 **contoso.com**）映射 *到* 用于 Web 应用的流量管理器域名 (**contoso.trafficmanager.net**)。
   
   > [!NOTE]
   > 另外，如果某条记录已被使用并且需要提前将应用绑定到该记录，可以创建一条额外的 CNAME 记录。 例如，要提前将 **www.contoso.com** 绑定到 Web 应用，请创建一条从 **awverify.www** 到 **contoso.trafficmanager.net** 的 CNAME 记录。 然后，可以将 "www.contoso.com" 添加到 Web 应用，而不更改 "www" CNAME 记录。 有关详细信息，请参阅 [Create DNS records for a web app in a custom domain][CREATEDNS]（在自定义域中为 web 应用创建 DNS 记录）。
   > 
   > 
4. 在注册机构添加或修改完 DNS 记录后，请保存这些更改。

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>启用流量管理器
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅 [Node.js 开发人员中心](/develop/nodejs/)。

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md



<!--HONumber=Nov16_HO3-->



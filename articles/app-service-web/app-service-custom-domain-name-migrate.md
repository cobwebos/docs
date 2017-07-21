---
title: "将活动自定义域迁移到 Azure 应用服务 | Microsoft Docs"
description: "了解如何在不停机的情况下，将已分配到实时站点的自定义域迁移到 Azure 应用服务中的应用。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: 5f6537a45bcb092b5ef463e8069b9fc5582c14c2
ms.contentlocale: zh-cn
ms.lasthandoff: 06/14/2017


---
# <a name="migrate-an-active-custom-domain-to-azure-app-service"></a>将活动自定义域迁移到 Azure 应用服务

本文介绍如何在不停机的情况下，将活动自定义域迁移到 [Azure应用服务](../app-service/app-service-value-prop-what-is.md)。

将实时站点及其域名迁移到应用服务时，该域名已在进行实时通信，而你不希望 DNS 解析在迁移过程中出现任何停机。 在这种情况下，请提前将域名绑定到用于域验证的 Azure 应用。

## <a name="prerequisites"></a>先决条件

本文假定你已了解如何[将自定义域手动映射到应用服务](app-service-web-tutorial-custom-domain.md)。

## <a name="bind-the-domain-name-preemptively"></a>提前绑定域名

提前绑定自定义域时，对 DNS 记录进行任何更改之前，需要完成以下两项操作：

- 验证域所有权
- 启用应用的域名

最终将 DNS 记录改为指向你的应用服务应用时，客户端将从旧站点重定向到应用服务应用，而不会因 DNS 解析而造成停机时间。

请遵循以下步骤进行配置：

1. 首先，按照[创建 DNS 记录](app-service-web-tutorial-custom-domain.md)中的步骤，使用 DNS 注册表创建验证 TXT 记录。
其他 TXT 记录遵循从 &lt;*子域*>.&lt;*根域*> 映射到 &lt;*appname*>.azurewebsites.net 的约定。
有关示例，请参见下表：  

    <table cellspacing="0" border="1">
    <tr>
    <th>FQDN 示例</th>
    <th>TXT 主机</th>
    <th>TXT 值</th>
    </tr>
    <tr>
    <td>contoso.com（根域）</td>
    <td>awverify.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>www.contoso.com（子域）</td>
    <td>awverify.www.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>\*.contoso.com（通配符域）</td>
    <td>awverify\*.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    </table>

2. 然后，按照[为应用启用自定义域名](app-service-web-tutorial-custom-domain.md#enable-a)中的步骤，将自定义域名添加到 Azure 应用。

    自定义域现已在 Azure 应用中启用。 只需通过域注册机构更新 DNS 记录。

3. 最后，更新域的 DNS 记录，以指向 Azure 应用，如[创建 DNS 记录](app-service-web-tutorial-custom-domain.md)中所示。

    DNS 传播开始后，应立即将用户流量重定向至 Azure 应用。

## <a name="next-steps"></a>后续步骤
请参阅[在 Azure 中购买 SSL 证书](web-sites-purchase-ssl-web-site.md)或[使用其他来源的 SSL 证书](app-service-web-tutorial-custom-ssl.md)，以了解如何使用 HTTPS 保护自定义域名。

> [!NOTE]
> 如果想要在注册 Azure 帐户之前开始使用 Azure 应用服务，请转到[试用应用服务](https://azure.microsoft.com/try/app-service/)，可以通过该页面在应用服务中立即创建一个生存期较短的入门 Web 应用。 不需要使用信用卡，也不需要做出承诺。
>
>

[开始使用 Azure DNS](../dns/dns-getstarted-create-dnszone.md)  
[在自定义域中为 web 应用创建 DNS 记录](../dns/dns-web-sites-custom-domain.md)  
[将域委托给 Azure DNS](../dns/dns-domain-delegation.md)


---
title: 迁移活动 DNS 名称
description: 了解如何在不停机的情况下，将已分配到实时站点的自定义 DNS 域名迁移到 Azure 应用服务。
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: 5c1760c746aca439e19ab5727e5be02f6dbad3cb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535683"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>将活动 DNS 名称迁移到 Azure 应用服务

本文介绍如何在不停机的情况下，将活动 DNS 名称迁移到 [Azure 应用服务](../app-service/overview.md)。

将实时站点及其 DNS 域名迁移到应用服务时，该 DNS 名称已在提供实时流量。 可以在迁移期间提前将活动 DNS 名称绑定到应用服务应用，从而避免 DNS 解析中的停机时间。

如果你不担心 DNS 解析中的停机时间，请参阅[将现有的自定义 DNS 名称映射到 Azure 应用服务](app-service-web-tutorial-custom-domain.md)。

## <a name="prerequisites"></a>先决条件

若要完成本操作说明：

- [确保应用服务应用不在“免费”层中](app-service-web-tutorial-custom-domain.md#checkpricing)。

## <a name="bind-the-domain-name-preemptively"></a>提前绑定域名

提前绑定自定义域时，对 DNS 记录进行任何更改之前，需要完成以下两项操作：

- 验证域所有权
- 启用应用的域名

最终将自定义 DNS 名称从旧站点迁移到应用服务应用时，DNS 解析中将不会有停机时间。

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>创建域验证记录

若要验证域所有权，请添加 TXT 记录。 TXT 记录从 awverify.&lt;subdomain>__ 映射到 &lt;appname>.azurewebsites.net__。 

你需要的 TXT 记录取决于要迁移的 DNS 记录。 有关示例，请参阅下表（`@` 通常表示根域）：

| DNS 记录示例 | TXT 主机 | TXT 值 |
| - | - | - |
| \@（根） | awverify__ | _&lt;应用程序名称>.azure 网站.net_ |
| www（子域） | awverify.www__ | _&lt;应用程序名称>.azure 网站.net_ |
| \*（通配符） | _验证。\*_ | _&lt;应用程序名称>.azure 网站.net_ |

在 DNS 记录页中，记下要迁移的 DNS 名称的记录类型。 应用服务支持来自 CNAME 和 A 记录的映射。

> [!NOTE]
> 对于某些提供程序（如 CloudFlare），`awverify.*` 不是有效记录。 只能改用 `*`。

> [!NOTE]
> 通配符 `*` 记录不会使用现有 CNAME 记录验证子域。 可能需要为每个子域显式创建一条 TXT 记录。


### <a name="enable-the-domain-for-your-app"></a>启用应用的域

在 [Azure 门户](https://portal.azure.com)中的应用页的左侧导航窗格中，选择“自定义域”****。 

![自定义域菜单](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

在“自定义域”**** 页中，选择“添加主机名”**** 旁的 + 图标****。

![添加主机名](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

键入已添加 TXT 记录的完全限定的域名，如 `www.contoso.com`。 对于通配符域（如 \*.contoso.com），可以使用与通配符域匹配的任何 DNS 名称。 

选择“验证”。 

“添加主机名”**** 按钮会被激活。 

请确保“主机名记录类型”**** 设置为你想要迁移的 DNS 记录类型。

选择“添加主机名”****。

![将 DNS 名称添加到应用](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

新主机名可能需要经过一段时间后才会反映在应用的“自定义域”页面中****。 请尝试刷新浏览器来更新数据。

![已添加 CNAME 记录](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

你的自定义 DNS 名称现已在 Azure 应用中启用。 

## <a name="remap-the-active-dns-name"></a>重新映射活动 DNS 名称

唯一需要做的是重新映射活动 DNS 记录以指向应用服务。 现在，它仍然指向旧站点。

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>复制应用的 IP 地址（仅限 A 记录）

如果你要重新映射 CNAME 记录，请跳过此部分。 

若要重新映射 A 记录，需要应用服务应用的外部 IP 地址，此地址显示在“自定义域”**** 页中。

通过选择右上角的 X **** 关闭“添加主机名”**** 页。 

在“自定义域”页中，复制应用的 IP 地址。 

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>更新 DNS 记录

返回到域提供商的 DNS 记录页，选择要重新映射的 DNS 记录。

对于 `contoso.com` 根域示例，重新映射 A 或 CNAME 记录，如下表中的示例所示： 

| FQDN 示例 | 记录类型 | 主机 | 值 |
| - | - | - | - |
| contoso.com（根域） | A | `@` | 在[复制应用的 IP 地址](#info)步骤中复制的 IP 地址 |
| www\.contoso.com (sub) | CNAME | `www` | _&lt;应用程序名称>.azure 网站.net_ |
| \*.contoso.com（通配符域） | CNAME | _\*_ | _&lt;应用程序名称>.azure 网站.net_ |

保存设置。

在 DNS 传播发生后，DNS 查询应立即开始解析应用服务应用。

## <a name="active-domain-in-azure"></a>Azure 中的活动域

可以在订阅之间或者在同一订阅内部迁移 Azure 中的活动自定义域。 但是，这种不停机的迁移方案需要在特定的时间为源应用和目标应用分配同一个自定义域。 因此，需确保两个应用未部署到同一个部署单元（在内部称为“Web 空间”）。 一个域名只能分配到每个部署单元中的一个应用。

可以通过查看 FTP/S URL `<deployment-unit>.ftp.azurewebsites.windows.net` 的域名，找到应用的部署单元。 检查并确保源应用与目标应用的部署单元不同。 应用的部署单元由它所在的[应用服务计划](overview-hosting-plans.md)决定。 该部署单元是在创建计划时由 Azure 随机选择的，且无法更改。 [在同一资源组和同一区域中创建两个计划](app-service-plan-manage.md#create-an-app-service-plan)时，Azure 只会确保这两个计划位于同一个部署单元，但不提供任何逻辑来确保计划位于不同的部署单元。** 在不同部署单元中创建计划的唯一方法是在新的资源组或区域中不断地创建计划，直到获得不同的部署单元。

## <a name="next-steps"></a>后续步骤

了解如何将自定义 TLS/SSL 证书绑定到应用服务。

> [!div class="nextstepaction"]
> [使用 Azure 应用服务中的 TLS 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)

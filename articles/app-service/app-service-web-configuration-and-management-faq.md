---
title: "Azure Web 应用的配置常见问题解答 | Microsoft Docs"
description: "获取有关 Azure App Service 的 Web 应用功能的配置和管理问题的常见问题解答。"
services: app-service\web
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 92cbc36ac2a566cf5dfbb2f7b3347973bab5ee8c
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2017
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Azure 中的 Web 应用配置和管理常见问题解答

本文包含有关 [Azure App Service 的 Web 应用功能](https://azure.microsoft.com/services/app-service/web/)的配置和管理问题的常见问题解答。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>如果要移动应用服务资源，则是否有我应该注意的限制？

如果你计划将应用服务资源移动到新的资源组或订阅，则需要注意几个限制。 有关详细信息，请参阅[应用服务限制](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations)。

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>如何为 Web 应用使用自定义域名？

有关将自定义域名用于 Azure Web 应用的常见问题解答，请参阅我们的七分钟视频[添加自定义域名](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name)。 该视频提供有关如何添加自定义域名的演练。 它介绍如何将自己的 URL（而不是 *.azurewebsites.net URL）用于应用服务 Web 应用。 还可以查看有关[如何映射自定义域名](app-service-web-tutorial-custom-domain.md)的详细演练。


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>如何为 Web 应用购买新的自定义域？

若要了解如何为应用服务 Web 应用购买和设置自定义域，请参阅[在应用服务中配置和配置自定义域名](custom-dns-web-site-buydomains-web-app.md)。


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>如何为 Web 应用上传和配置现有 SSL 证书？

若要了解如何上传和设置现有自定义 SSL 证书，请参阅[将现有自定义 SSL 证书绑定到 Azure Web 应用](app-service-web-tutorial-custom-ssl.md#upload)。


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>如何在 Azure 中为 Web 应用购买和配置新的 SSL 证书？

若要了解如何为应用服务 Web 应用购买和设置 SSL 证书，请参阅[将 SSL 证书添加到应用服务应用](web-sites-purchase-ssl-web-site.md)。


## <a name="how-do-i-move-application-insights-resources"></a>如何移动 Application Insights 资源？

当前，Azure Application Insights 不支持移动操作。 如果原始资源组包括 Application Insights 资源，则无法移动该资源。 如果在尝试移动应用服务应用时包括了 Application Insights 资源，则整个移动操作会失败。 不过，即使 Application Insights 和应用服务计划不与应用位于同一资源组中，应用也可以正常运行。

有关详细信息，请参阅[应用服务限制](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations)。

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>我可以在何处找到指导清单并详细了解资源移动操作？

[应用服务限制](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations)演示如何将资源移到新订阅，或移到同一个订阅中的新资源组。 可以获取有关资源移动清单的信息、了解支持移动操作的服务以及详细了解应用服务限制和其他主题。

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>如何为 Web 应用设置服务器时区？

为 Web 应用设置服务器时区：

1. 在 Azure 门户中的应用服务订阅中，转到“应用程序设置”菜单。
2. 在“应用设置”下，添加此设置：
    * 键 = WEBSITE_TIME_ZONE
    * 值 = *所需时区*
3. 选择“保存”。

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>为何我的连续 Web 作业有时会失败？

默认情况下，Web 应用如果已处于空闲状态达到一定时间，则会卸载。 这样可以让系统节省资源。 在基本和标准计划中，可以打开“始终可用”设置以使 Web 应用始终加载。 如果 Web 应用运行连续 Web 作业，则应打开“始终打开”，否则 Web 作业可能无法可靠运行。 有关详细信息，请参阅[创建连续运行的 Web 作业](web-sites-create-web-jobs.md#CreateContinuous)。

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>如何获取 Web 应用的出站 IP 地址？

获取 Web 应用的出站 IP 地址列表：

1. 在 Azure 门户中的 Web 应用边栏选项卡，转到“属性”菜单。
2. 搜索“出站 ip 地址”。

出站 IP 地址列表随即出现。

如果你的网站在适用于 PowerApps 的应用服务环境中承载，则若要了解如何获取出站 IP 地址，请参阅[出站网络地址](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses)。

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>如何获取 Web 应用的保留或专用入站 IP 地址？

若要为针对 Azure 应用网站进行的入站调用设置专用或保留 IP 地址，请安装和配置基于 IP 的 SSL 证书。

请注意，若要将专用或保留 IP 地址用于入站调用，应用服务计划必须处于基本或更高服务计划中。

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>是否可以导出应用服务证书以在 Azure 外部使用（如用于在其他位置承载的网站）？ 

应用服务证书被视为 Azure 资源。 不应在 Azure 服务外部使用它们。 无法导出它们以在 Azure 外部使用。 有关详细信息，请参阅[应用服务证书和自定义域的常见问题解答](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)。

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>是否可以导出应用服务证书以用于其他 Azure 云服务？

门户针对通过 Azure Key Vault 将应用服务证书部署到应用服务应用提供一流的体验。 但是，我们从客户处收到了在应用服务平台外部使用这些证书（例如，用于 Azure 虚拟机）的请求。 若要了解如何创建应用服务证书的本地 PFX 副本以便可以将证书用于其他 Azure 资源，请参阅[创建应用服务证书的本地 PFX 副本](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)。

有关详细信息，请参阅[应用服务证书和自定义域的常见问题解答](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)。


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>当我尝试备份 Web 应用时，为何看到消息“已部分成功”？

备份失败的一个常见原因是某些文件在由应用程序使用。 执行备份时，在使用的文件会锁定。 这会阻止备份这些文件，可能会导致“已部分成功”状态。 可以通过从备份过程中排除文件来防止此情况发生。 可以选择仅备份所需内容。 有关详细信息，请参阅[仅随 Azure Web 应用备份站点的重要部分](http://www.zainrizvi.io/2015/06/05/creating-partial-backups-of-your-site-with-azure-web-apps/)。

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>如何从 HTTP 响应中删除标头？

若要从 HTTP 响应中删除标头，请更新站点的 web.config 文件。 有关详细信息，请参阅[在 Azure 网站上删除标准服务器标头](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)。

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>应用服务是否符合 PCI 标准 3.0 和 3.1？

当前，Azure App Service 的 Web 应用功能符合 PCI 数据安全标准 (DSS) 版本 3.0 级别 1。 PCI DSS 版本 3.1 处于我们的路线图上。 我们已在计划如何继续采用最新标准。

PCI DSS 版本 3.1 认证要求禁用传输层安全性 (TLS) 1.0。 当前，对于大多数应用服务计划，不会选择禁用 TLS 1.0。 但是，如果使用应用服务环境，或者愿意将工作负荷迁移到应用服务环境，则可以更好地控制环境。 这涉及到通过与 Azure 支持部门联系来禁用 TLS 1.0。 在不久的将来，我们计划使用户可以访问这些设置。

有关详细信息，请参阅 [Microsoft Azure App Service Web 应用符合 PCI 标准 3.0 和 3.1](https://support.microsoft.com/help/3124528)。

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>如何使用过渡环境和部署槽位？

在标准和高级应用服务计划中，将 Web 应用部署到应用服务时，可以部署到单独的部署槽位而不是默认的生产槽位。 部署槽是具有自己的主机名的动态 Web 应用。 两个部署槽（包括生产槽）之间的 Web 应用内容与配置元素可以交换。

有关使用部署槽位的详细信息，请参阅[在应用服务中设置过渡环境](web-sites-staged-publishing.md)。

## <a name="how-do-i-access-and-review-webjob-logs"></a>如何访问和查看 Web 作业日志？

查看 Web 作业日志：

1. 登录到 [Kudu 网站](https://*yourwebsitename*.scm.azurewebsites.net)。
2. 选择 Web 作业。
3. 选择“切换输出”按钮。
4. 若要下载输出文件，请选择“下载”链接。
5. 对于单个运行，选择“单个调用”。
6. 选择“切换输出”按钮。
7. 选择下载链接。

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>我在尝试对 SQL Server 使用混合连接。 为何会看到消息“System.OverflowException: 算术运算导致溢出”？

如果使用混合连接访问 SQL Server，则 2016 年 5 月 10 的 Microsoft.NET 更新可能会导致连接失败。 你可能会看到此消息：

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>解决方法

该异常是由于混合连接管理器存在问题而导致，该问题现已修复。 请务必[更新混合连接管理器](https://go.microsoft.com/fwlink/?LinkID=841308)以解决此问题。

## <a name="how-do-i-add-or-edit-a-url-rewrite-rule"></a>如何添加或编辑 URL 重写规则？

添加或编辑 URL 重写规则：

1. 设置 Internet Information Services (IIS) 管理器，以便它连接到应用服务 Web 应用。 若要了解如何将 IIS 管理器连接到应用服务，请参阅[使用 IIS 管理器远程管理 Azure 网站](https://azure.microsoft.com/blog/remote-administration-of-windows-azure-websites-using-iis-manager/)。
2. 在 IIS 管理器中，添加或编辑 URL 重写规则。 若要了解如何添加或编辑 URL 重写规则，请参阅[为 URL 重写模块创建重写规则](https://www.iis.net/learn/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)。

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>如何控制应用服务的入站流量？

在站点级别，有两个选项可用于控制应用服务的入站流量：

* 打开动态 IP 限制。 若要了解如何打开动态 IP 限制，请参阅[针对 Azure 网站的 IP 和域限制](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)。
* 打开模块安全。 若要了解如何打开模块安全，请参阅 [Azure 网站上的 ModSecurity Web 应用程序防火墙](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/)。

如果使用应用服务环境，则可以使用 [Barracuda 防火墙](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/)。

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>如何在应用服务 Web 应用中阻止端口？

在应用服务共享租户环境中，由于基础结构的性质，因此无法阻止特定端口。 TCP 端口 4016、4018 和 4020 也可以打开以用于 Visual Studio 远程调试。

在应用服务环境中，可完全控制入站和出站流量。 可以使用网络安全组限制或阻止特定端口。 有关应用服务环境的详细信息，请参阅[应用服务环境简介](https://azure.microsoft.com/blog/introducing-app-service-environment/)。

## <a name="how-do-i-capture-an-f12-trace"></a>如何捕获 F12 跟踪？

有两个选项可用于捕获 F12 跟踪：

* F12 HTTP 跟踪
* F12 控制台输出

### <a name="f12-http-trace"></a>F12 HTTP 跟踪

1. 在 Internet Explorer 中，转到你的网站。 请务必先登录，然后再执行后续步骤。 否则，F12 跟踪会捕获敏感的登录数据。
2. 按 F12。
3. 验证是否选择了“网络”选项卡，然后选择绿色的“播放”按钮。
4. 执行再现问题的步骤。
5. 选择红色的“停止”按钮。
6. 选择“保存”按钮（磁盘图标），然后保存 HAR 文件（在 Internet Explorer 和 Edge 中），或右键单击 HAR 文件，然后选择“保存为包含内容的 HAR”（在 Chrome 中）。

### <a name="f12-console-output"></a>F12 控制台输出

1. 选择“控制台”选项卡。
2. 对于包含的项数不为零的每个选项卡，选择选项卡（“错误”、“警告”或“信息”）。 如果未选择选项卡，则选项卡图标会在光标离开它时为灰色或黑色。
3. 在窗格的消息区域中右键单击，然后选择“全部复制”。
4. 在文件中粘贴复制的文本，然后保存文件。

若要查看 HAR 文件，可以使用 [HAR 查看器](http://www.softwareishard.com/har/viewer/)。

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>在我尝试将应用服务 Web 应用到连接到与 ExpressRoute 连接的虚拟网络时，为何会遇到错误？

如果尝试将 Azure Web 应用连接到与 Azure ExpressRoute 连接的虚拟网络，则会失败。 会显示以下消息：“网关不是 VPN 网关”。

当前，无法与连接到 ExpressRoute 的虚拟网络建立点到站点 VPN 连接。 对于同一虚拟网络而言，点到站点 VPN 和 ExpressRoute 不能共存。 有关详细信息，请参阅 [ExpressRoute 和站点到站点 VPN 连接限制和局限性](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations)。

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>如何将应用服务 Web 应用连接到具有静态路由（基于策略）网关的虚拟网络？

当前，不支持将应用服务 Web 应用连接到具有静态路由（基于策略）网关的虚拟网络。 如果目标虚拟网络已经存在，必须在连接到应用之前借助动态路由网关使网络处于点到站点 VPN 启用状态。 如果网关设置为静态路由，则无法启用点到站点 VPN。 

有关详细信息，请参阅[将应用与 Azure 虚拟网络进行集成](web-sites-integrate-with-vnet.md#getting-started)。

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>在应用服务环境中，为何即使有两个可用的辅助角色，也只能创建一个应用服务计划？

为了提供容错能力，应用服务环境要求每个辅助角色池至少需要一个额外的计算资源。 无法为额外的计算资源分配工作负荷。

有关详细信息，请参阅[如何创建应用服务环境](environment/app-service-web-how-to-create-an-app-service-environment.md)。

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>在尝试创建应用服务环境时，为何遇到超时？

优势，创建应用服务环境会失败。 在这种情况下，你会在活动日志中看到以下错误：
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period.”}}
```

若要解决此问题，请确保不存在以下任何情况：
* 子网太小。
* 子网非空。
* ExpressRoute 会阻碍应用服务环境的网络连接要求。
* 错误的网络安全组会阻碍应用服务环境的网络连接要求。
* 强制隧道处于打开状态。

有关详细信息，请参阅[部署（创建）新 Azure 应用服务环境时的常见问题](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/)。

## <a name="why-cant-i-delete-my-app-service-plan"></a>为何无法删除应用服务计划？

如果有任何应用服务应用与应用服务计划关联，则无法删除应用服务计划。 删除应用服务计划之前，应从应用服务计划中删除所有关联的应用服务应用。

## <a name="how-do-i-schedule-a-webjob"></a>如何计划 Web 作业？

可以使用 Cron 表达式创建计划 Web 作业：

1. 创建 settings.job 文件。
2. 在此 JSON 文件中，使用 Cron 表达式包含计划属性： 
    ```
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

有关计划 Web 作业的详细信息，请参阅[使用 Cron 表达式创建计划 Web 作业](web-sites-create-web-jobs.md#CreateScheduledCRON)。

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>如何对应用服务应用执行渗透测试？

若要执行渗透测试，请[提交请求](https://security-forms.azure.com/penetration-testing/terms)。

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>如何为使用流量管理器的应用服务 Web 应用配置自定义域名？

若要了解如何对使用 Azure 流量管理器进行负载平衡的应用服务应用使用自定义域名，请参阅[为使用流量管理器的 Azure Web 应用配置自定义域名](web-sites-traffic-manager-custom-domain-name.md)。

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>我的应用服务证书被标记为存在欺诈。 如何解决此问题？

在应用服务证书购买的域验证过程中，可能会看到以下消息：

“你的证书已被标记为可能存在欺诈。 请求当前正在审查中。 如果证书未在 24 小时内变为可用，请联系 Azure 支持部门。”

如该消息所示，此欺诈验证过程可能需要最多 24 小时才能完成。 在此期间，你会继续看到该消息。

如果你的应用服务证书在 24 小时后继续显示此消息，请运行以下 PowerShell 脚本。 该脚本会联系[证书提供商](https://www.godaddy.com/)直接解决问题。

```
Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzureRmResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>应用服务中的身份验证和授权如何工作？

有关应用服务中的身份验证和授权的详细文档，请参阅各种标识提供者登录的文档：
* [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Google](app-service-mobile-how-to-configure-google-authentication.md)
* [Microsoft 帐户](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>如何将默认的 *.azurewebsites.net 域重定向到我的 Azure Web 应用的自定义域？

在 Azure 中使用 Web 应用创建新网站时，会向站点分配默认的 sitename.azurewebsites.net 域。 如果将自定义主机名添加到站点，并且不希望用户能够访问默认的 *.azurewebsites.net 域，则可以重定向默认 URL。 若要了解如何将所有流量从网站的默认域重定向到自定义域，请参阅[在 Azure Web 应用中将默认域重定向到自定义域](http://www.zainrizvi.io/2016/04/07/block-default-azure-websites-domain/)。

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>如何确定应用服务中安装的 .NET 版本？

用于查找应用服务中安装的 Microsoft .NET 版本的最快方法是使用 Kudu 控制台。 可以从门户或使用应用服务应用的 URL，来访问 Kudu 控制台。 有关详细说明，请参阅[确定应用服务中安装的 .NET 版本](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/)。

## <a name="why-isnt-autoscale-working-as-expected"></a>为何自动缩放不按预期方式工作？

如果 Azure 自动缩放未按预期方式缩小或扩大 Web 应用实例，则你可能会遇到我们有意选择不缩放以避免由于“波动”而导致的无限循环的情况。 当扩大与缩小阈值之间没有足够空间时，通常会发生这种情况。 若要了解如何避免“波动”以及如何了解其他自动缩放最佳做法，请参阅[自动缩放最佳做法](../monitoring-and-diagnostics/insights-autoscale-best-practices.md#autoscale-best-practices)。

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>为何自动缩放有时只部分缩放？

当指标超过预配置边界时，会触发自动缩放。 有时，你可能会注意到与期望相比，只填充了部分容量。 当所需数量的实例不可用时，可能会发生这种情况。 在这种情况下，自动缩放使用可用数量的实例进行部分填充。 自动缩放随后运行重新平衡逻辑以获取更多容量。 它会分配剩余实例。 请注意，这可能需要几分钟。

如果在几分钟后未看到预期数量的实例，则可能是因为部分重填已足以使指标处于边界内。 或者，自动缩放可能已减少，因为它已达到指标下限。

如果任何这些情况都不适用并且问题仍然存在，请提交支持请求。

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>如何为我的内容打开 HTTP 压缩？

若要同时为静态和动态内容类型打开压缩，请将以下代码添加到应用程序级 web.config 文件：

```
<system.webServer>
<urlCompression doStaticCompression="true" doDynamicCompression="true" />
< /system.webServer>
```

还可以指定要压缩的特定动态和静态 MIME 类型。 有关详细信息，请参阅我们在[简单 Azure 网站上的 httpCompression 设置](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview)中对论坛问题的回复。

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>如何从本地环境迁移到应用服务？

若要将站点从 Windows 和 Linux Web 服务器迁移到应用服务，可以使用 Azure App Service 迁移助手。 该迁移工具会根据需要在 Azure 中创建 Web 应用和数据库，然后发布内容。 有关详细信息，请参阅 [Azure App Service 迁移助手](https://www.migratetoazure.net/)。

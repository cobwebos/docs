<properties title="了解如何配置使用流量管理器的 Azure 网站，以使用借助 GoDaddy 注册的域名" pageTitle="为使用流量管理器的 Azure 网站配置 GoDaddy 域名" metaKeywords="Azure, Azure  Websites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

# 使用流量管理器为 Azure 网站配置自定义域名 (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-cn/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自定义域</a><a href="/zh-cn/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/zh-cn/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/zh-cn/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-cn/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-cn/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-cn/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-cn/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-cn/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-cn/documentation/articles/web-sites-godaddy-custom-domain-name/" title=" Websites"> 网站</a> | <a href="/zh-cn/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title=" Website using Traffic Manager" class="current"> 使用流量管理器的网站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文提供了如何将从[Go Daddy](https://godaddy.com)购买的自定义域名用于 Azure 网站的说明。

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文内容：

-   [了解 DNS 记录](#understanding-records)
-   [将网站配置为标准模式](#bkmk_configsharedmode)
-   [为自定义域添加 DNS 记录](#bkmk_configurecname)
-   [为网站启用流量管理器](#enabledomain)

<h2><a name="understanding-records"></a>了解 DNS 记录</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>将网站配置为标准模式</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>为自定义域添加 DNS 记录</h2>

若要将自定义域与 Azure 网站关联，必须使用 GoDaddy 提供的工具在 DNS 表中为自定义域添加新条目。使用以下步骤找到用于 GoDaddy.com 的 DNS 工具

1. 通过 GoDaddy.com 登录你的帐户，选择**我的帐户**，然后选择**管理你的域**。最后，选择要用于你的 Azure 网站的域名。

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. 从**域详细信息**页面上，选择**DNS 区域文件**选项卡。此部分用于添加和修改用于你的域名的 DNS 记录。选择**编辑**按钮以显示**区域文件编辑器**。

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

4. **区域文件编辑器**为各个记录类型划分为不同部分，从 A 记录开始（作为第一部分列为**A (主机)**，后面是 CNAME 记录（列为**CNAME (别名)**）。若要添加新条目，请使用对应部分下面的**快速添加**按钮。若要编辑现有条目，请选择该条目并修改现有信息。

	![zone file editor](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)

	> [AZURE.NOTE] 在给区域文件添加条目之前，请注意，GoDaddy 已经为常用子域（在编辑器中被称为**主机**）创建了 DNS 记录，如**电子邮件**、**文件**、**邮件**，以及其他。如果你要使用的名称已经存在，请修改现有的记录，而不是创建新记录。

	添加 CNAME 记录时，必须将**主机**字段设置为你要使用的子域。例如，**www**。你必须将**指向**字段设置为用于 Azure 网站的流量管理器配置文件的**.trafficmanager.cn**域名。例如，**contoso.trafficmanager.cn**。

	> [AZURE.NOTE] 在将你的自定义域名与使用流量管理器进行负载平衡的网站相关联时，只能使用 CNAME 记录。

5. 完成添加或修改记录之后，请单击**保存区域文件**，以保存这些更改。

<h2><a name="enabledomain"></a>启用流量管理器网站</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

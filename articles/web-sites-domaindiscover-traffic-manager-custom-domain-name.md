<properties title="了解如何将使用流量管理器的 Azure 网站配置为使用在 DomainDiscover 注册的域名 - TierraNet" pageTitle="为 Azure 配置 DomainDiscover 域名  使用流量管理器的网站" metaKeywords="Windows Azure, Windows Azure  Websites, DomainDiscover, TierraNet, Traffic Manager" description="了解如何将使用流量管理器的 Azure 网站配置为使用在 DomainDiscover 注册的域名 - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

# 使用流量管理器为 Microsoft Azure 网站配置自定义域名 (DomainDiscover / TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-cn/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自定义域</a><a href="/zh-cn/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-cn/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/zh-cn/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-cn/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-cn/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-cn/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-cn/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/zh-cn/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-cn/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Websites">网站</a> | <a href="/zh-cn/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">使用流量管理器的网站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文提供了如何将从 [DomainDiscover.com](https://domaindiscover.com) 购买的自定义域名用于 Azure 网站的说明。DomainDiscover.com 是 [TierraNet](https://www.tierra.net/) 的组成部分。

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

若要将自定义域与 Azure 网站关联，必须使用 DomainDiscover 提供的工具在 DNS 表中为自定义域添加新条目。使用以下步骤找到用于 DomainDiscover.com 的 DNS 工具

1. 从"登录"菜单中选择"控制面板"，通过 DomainDiscover.com (TierraNet) 登录到你的帐户。

    ![DomainDiscover Login Menu](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2. 在"域服务"页面上，选择要用于你的 Azure 网站的域。

    ![Domain management page](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3. 在域设置中，单击"DNS 服务"的"编辑"按钮。

    ![DNS edit button](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4. 在"管理 DNS"窗口中，选择要添加到"添加记录"列表中的 DNS 记录的类型。然后单击"添加"按钮。

    ![DNS edit button](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5. 在下面的页面上，输入 DNS 记录值。然后单击"添加"按钮。

    ![DNS edit button](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords_TM.png)

    * 在添加 CNAME 记录时，必须先在"管理 DNS"页面上选择"CNAME (别名)"。然后将"主机"字段设置为你要使用的子域。例如，**www**。必须将"别名主机"字段设置为用于 Azure 网站的流量管理器配置文件的".trafficmanager.cn"域名。例如，**contoso.trafficmanager.cn**。然后提供生存时间 (TTL) 值，如 1800 秒。

	    > [AZURE.NOTE] 在将你的自定义域名与使用流量管理器进行负载平衡的网站相关联时，只能使用 CNAME 记录。

<h2><a name="enabledomain"></a>启用流量管理器网站</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

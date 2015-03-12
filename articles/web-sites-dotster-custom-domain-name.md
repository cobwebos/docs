<properties title="了解如何将 Azure 网站配置为使用在 Dotster 注册的域名" pageTitle="为 Azure 网站配置 Dotster 域名" metaKeywords="Azure, Azure  Websites, Dotster" description="了解如何将 Azure 网站配置为使用在 Dotster 注册的域名" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

# 为 Azure 网站配置自定义域名 (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-cn/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自定义域</a><a href="/zh-cn/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-cn/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/zh-cn/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-cn/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-cn/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-cn/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/zh-cn/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-cn/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-cn/documentation/articles/web-sites-dotster-custom-domain-name/" title=" Websites" class="current"> 网站</a> | <a href="/zh-cn/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title=" Website using Traffic Manager"> 使用流量管理器的网站</a></div>
[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

本文提供了如何将从 [Dotster.com](https://dotster.com) 购买的自定义域名用于 Azure 网站的说明。

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文内容：

-   [了解 DNS 记录](#understanding-records)
-   [为基本、共享或标准模式配置网站](#bkmk_configsharedmode)
-   [为自定义域添加 DNS 记录](#bkmk_configurecname)
-   [在网站上启用域](#enabledomain)

<h2><a name="understanding-records"></a>了解 DNS 记录</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>将网站配置为基本、共享或标准模式</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>为自定义域添加 DNS 记录</h2>

若要将自定义域与 Azure 网站关联，必须使用 Dotster 提供的工具在 DNS 表中为自定义域添加新条目。使用以下步骤找到用于 Dotster.com 的 DNS 工具

1. 使用你的 Dotster.com 帐户登录。在"域"菜单中，选择"DomainCentral"。

    ![Domain Central Dotster Menu](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png)

2. 选择你的域，以显示设置列表。然后选择 **Nameservers** 链接。

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png)

3. 选择"使用不同的名称服务器"。为了利用 Dotster 上的 DNS 服务，你必须指定以下名称服务器：ns1.nameresolve.com、ns2.nameresolve.com、ns3.nameresolve.com 和 ns4.nameresolve.com。

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png)

    > [AZURE.NOTE] 名称服务器更改生效可能需要 24-48 小时。本文中其余步骤在名称服务器更改生效后才起作用。

4. 在 DomainCentral 中，选择你的域，然后选择 **DNS**。在"修改"列表中，选择要添加的 DNS 记录的类型（"CNAME 别名"或"A 记录"）。 

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png)

5. 然后指定该记录的"主机"和"指向"字段。完成时，单击"添加"按钮。

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME.png)
 
    * 添加 CNAME 记录时，必须将"主机"字段设置为你要使用的子域。例如，**www**。必须将"指向"字段设置为你的 Azure 网站的 **.chinacloudsites.cn** 域名。例如 **contoso.chinacloudsites.cn**。

    * 在添加 A 记录时，必须将"主机"字段设置为 **@**（代表根域名，如 **contoso.com**）或者是你要使用的子域（例如 **www**）。必须将"指向"字段设置为你的 Azure 网站的 IP 地址。

		> [AZURE.NOTE] 如果你要使用 A 记录，则还必须使用以下配置之一添加 CNAME 记录：
		> 
		> * "指向"**&lt;yourWebsitename&gt;.chinacloudsites.cn** 值的"主机"值 www。
		> 
		> 或者
		> 
		> * "指向"**awverify.&lt;yourWebsitename&gt;.chinacloudsites.cn** 值的"主机"值 www。
		> 
		> 此 CNAME 记录由 Azure 用来验证你拥有 A 记录所描述的域


<h2><a name="enabledomain"></a>在网站上启用域名</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

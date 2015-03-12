<properties title="了解如何将 Azure 网站配置为使用在 Directnic 注册的域名" pageTitle="为 Azure 网站配置 Directnic 域名" metaKeywords="Azure, Azure  Websites, Directnic" description="了解如何将 Azure 网站配置为使用在 Directnic 注册的域名" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

# 为 Azure 网站配置自定义域名 (Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-cn/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自定义域</a><a href="/zh-cn/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-cn/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/zh-cn/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-cn/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-cn/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-cn/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-cn/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-cn/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-cn/documentation/articles/web-sites-directnic-custom-domain-name/" title=" Websites" class="current"> 网站</a> | <a href="/zh-cn/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title=" Website using Traffic Manager"> 使用流量管理器的网站</a></div>
[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]


[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

本文提供了如何将从 [DirectNic.com](https://directnic.com) 购买的自定义域名用于 Azure 网站的说明。

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

若要将自定义域与 Azure 网站关联，必须使用 Directnic 提供的工具在 DNS 表中为自定义域添加新条目。使用以下步骤找到用于 Directnic.com 的 DNS 工具

1. 通过 Directnic.com 登录你的帐户，选择"我的服务"，然后选择"域"。

    ![Directnic Services Menu](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2. 单击要用于你的 Azure 网站的域名。

2. 在域管理页面的"服务"窗格中单击 **DNS** 的"管理"按钮。

    ![Services Panel](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4. 通过填写"类型"、"名称"和"数据"字段，添加 DNS 记录。完成时，单击"添加记录"按钮。   

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png)

    * 添加 CNAME 记录时，必须将"名称"字段设置为你要使用的子域。例如，**www**。必须将"数据"字段设置为你的 Azure 网站的 **.chinacloudsites.cn** 域名  。例如 **contoso.chinacloudsites.cn**。

    * 在添加 A 记录时，必须将"名称"字段设置为 **@**（代表根域名，如 **contoso.com**）或者是你要使用的子域（例如 **www**）。必须将"数据"字段设置为你的 Azure 网站的 IP 地址。

		> [AZURE.NOTE] 如果你要使用 A 记录，则还必须使用以下配置之一添加 CNAME 记录：
		> 
		> * "名称"值 **www** 以及"数据"值 **&lt;yourwebsitename&gt;.chinacloudsites.cn**。
		> 
		> 或者
		> 
		> * "名称"值 **awverify.www** 以及"数据"值 **awverify.&lt;yourWebsitename&gt;.chinacloudsites.cn**。
		> 
		> 此 CNAME 记录由 Azure 用来验证你拥有 A 记录所描述的域


<h2><a name="enabledomain"></a>在网站上启用域名</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

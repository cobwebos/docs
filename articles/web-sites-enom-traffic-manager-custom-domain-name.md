<properties title="了解如何将使用流量管理器的 Azure 网站配置为使用在 eNom 注册的域名" pageTitle="为使用流量管理器的 Azure 网站配置 eNom 域名" metaKeywords="Windows Azure, Windows Azure  Websites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

# 为使用流量管理器的 Azure 网站配置自定义域名 (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-cn/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自定义域</a><a href="/zh-cn/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-cn/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/zh-cn/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-cn/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/zh-cn/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-cn/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-cn/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-cn/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-cn/documentation/articles/web-sites-enom-custom-domain-name/" title="Websites">网站</a> | <a href="/zh-cn/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">网站配置 Dotster 域名</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文提供了如何将从 [eNom](https://enom.com) 购买的自定义域名用于 Azure 网站的说明。

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

若要将自定义域与 Azure 网站关联，必须使用 eNom 提供的工具在 DNS 表中为自定义域添加新条目。使用以下步骤找到用于 eNom.com 的 DNS 工具

1. 通过 eNom 登录你的帐户，选择"域"，然后选择"我的域"。这样就会显示你的域名。

2. 从"我的域"页面上，使用"管理域"字段来选择"主机记录"。这样就会显示主机记录字段。

	![DNS Zone File tab](./media/web-sites-custom-domain-name/e-hostrecords.png)

4. 利用"主机记录"编辑器，你可以使用"记录类型"字段选择特定的记录类型。对于使用流量管理器的 Azure 网站，只应选择使用"CNAME (别名)"，因为流量管理器只能用于 CNAME 记录。

	![zone file editor](./media/web-sites-custom-domain-name/e-editrecordstm.png)

	* 添加 CNAME 记录时，必须将"主机名"字段设置为你要使用的子域。例如，**www**。必须将"地址"字段设置为用于 Azure 网站的流量管理器配置文件的 **.trafficmanager.cn** 域名。例如，**contoso.trafficmanager.cn**。

5. 完成添加或修改记录之后，请单击"保存"，以保存这些更改。

<h2><a name="enabledomain"></a>启用流量管理器网站</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

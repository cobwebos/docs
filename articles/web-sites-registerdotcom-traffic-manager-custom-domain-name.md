<properties title="了解如何配置使用流量管理器的 Azure 网站来使用 Register.com 中注册的域名" pageTitle="为使用流量管理器的 Azure 网站配置 Register.com 域名" metaKeywords="Windows Azure, Windows Azure Websites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

# 为使用流量管理器的 Azure 网站配置自定义域名 (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-cn/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自定义域</a><a href="/zh-cn/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-cn/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/zh-cn/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/zh-cn/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-cn/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-cn/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-cn/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-cn/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-cn/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title=" Websites"> 网站</a> | <a href="/zh-cn/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title=" Website using Traffic Manager" class="current"> 使用流量管理器的网站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文提供了如何将从 [Register.com](https://register.com) 购买的自定义域名用于 Azure 网站的说明。

[WACOM.INCLUDE [tm Websitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文内容：

-   [了解 DNS 记录](#understanding-records)
-   [将您的网站配置为标准模式](#bkmk_configsharedmode)
-   [为自定义域添加 DNS 记录](#bkmk_configurecname)
-   [为您的网站启用流量管理器](#enabledomain)

<h2><a name="understanding-records"></a>了解 DNS 记录</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>将您的网站配置为标准模式</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>为自定义域添加 DNS 记录</h2>

要将自定义域与 Azure 网站关联，必须通过使用 Register.com 提供的工具在 DNS 表中为自定义域添加新条目。使用以下步骤找到并使用这些 DNS 工具。

1. 在 register.com 上登录到你的帐户，然后在右上角选择**您的帐户**以查看你的域，然后选择你的自定义域名。

	![the my account page](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

3. 向下滚动页面直到显示**高级技术设置**。这部分中的链接可用于管理你的域的记录。对于 CNAME 记录，请使用**编辑域别名记录**链接。

	![Advanced technical settings](./media/web-sites-custom-domain-name/rdotcom-advancedsettingstm.png)

5. 单击**编辑**按钮时，将看到一个表单，可用于修改现有记录，或添加新记录。对于 CNAME 和 A 两种记录，表单都是类似的。

	* 添加 CNAME 记录时，必须将 **.mydomainname.com** 字段设置为你要使用的子域。例如，**www**。你必须将**指向**值选为用于 Azure 网站的流量管理器配置文件的 **.trafficmanager.cn** 域名。例如，**contoso.trafficmanager.cn**。将**引用主机名称**保留为**选择**，因为在创建用于 Azure 网站的 CNAME 记录时此字段不是必填字段。
	
		![cname form](./media/web-sites-custom-domain-name/rdotcom-editcnamerecordtm.png)


5. 完成添加或修改记录之后，请单击**继续**，以查看这些更改。再次选择**继续**，以保存这些更改。

<h2><a name="enabledomain"></a>启用流量管理器网站</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

<properties title="了解如何配置 Azure 网站以使用 Register.com 中注册的域名" pageTitle="为 Azure 网站配置 Register.com 域名" metaKeywords="Azure, Azure Websites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

# 为 Azure 网站 (Register.com) 配置自定义域名

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-cn/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自定义域</a><a href="/zh-cn/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-cn/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/zh-cn/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/zh-cn/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-cn/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-cn/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-cn/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-cn/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-cn/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title=" Websites" class="current"> 网站</a> | <a href="/zh-cn/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title=" Website using Traffic Manager"> 使用流量管理器的网站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

本文提供了如何将从 [Register.com](https://register.com) 购买的自定义域名用于 Azure 网站的说明。

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文内容：

-   [了解 DNS 记录](#understanding-records)
-   [将网站配置为基本、共享或标准模式](#bkmk_configsharedmode)
-   [为自定义域添加 DNS 记录](#bkmk_configurecname)
-   [在您的网站上启用域](#enabledomain)

<h2><a name="understanding-records"></a>了解 DNS 记录</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>将网站配置为基本、共享或标准模式</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>为自定义域添加 DNS 记录</h2>

要将自定义域与 Azure 网站关联，必须通过使用 Register.com 提供的工具在 DNS 表中为自定义域添加新条目。使用以下步骤找到并使用这些 DNS 工具。

1. 在 register.com 上登录到你的帐户，然后在右上角选择**您的帐户**以查看你的域，然后选择你的自定义域名。

	![the my account page](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

3. 向下滚动页面直到显示**高级技术设置**。这部分中的链接可用于管理你的域的记录。

	* 对于 A 记录，请使用**编辑 IP 地址记录**链接。
	* 对于 CNAME 记录，请使用**编辑域别名记录**链接。

	![Advanced technical settings](./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png)

5. 单击**编辑**按钮时，将看到一个表单，可用于修改现有记录，或添加新记录。对于 CNAME 和 A 两种记录，表单都是类似的。

	* 添加 CNAME 记录时，必须将 **.mydomainname.com** 字段设置为你要使用的子域。例如，**www**。必须将**指向**值选为 Azure 网站的 **.chinacloudsites.cn** 域名。例如，**contoso.chinacloudsites.cn**。将**引用主机名称**保留为**选择**，因为在创建用于 Azure 网站的 CNAME 记录时此字段不是必填字段。
	
		![cname form](./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png)

		> [AZURE.NOTE] 如果你要使用 A 记录，则还必须使用以下配置之一添加 CNAME 记录：
		> 
		> * **别名**值为 **www**，**其他主机**值为 **&lt;yourwebsitename&gt;.chinacloudsites.cn**。
		> 
		> 或者
		> 
		> * **别名**值为 **awverify.www**，**其他主机**值为 **awverify.&lt;yourwebsitename&gt;.chinacloudsites.cn**。
		> 
		> 此 CNAME 记录由 Azure 用来验证你拥有 A 记录所描述的域。

	* 添加 A 记录时，必须将 **.mydomainname.com** 字段设置为你要使用的子域（例如 **www**）。该字段保留为空，以设置根域，或使用 **\** 来创建通配符映射。必须将**指向**字段设置为 Azure 网站的 IP 地址。

		![a record form](./media/web-sites-custom-domain-name/rdotcom-editarecord.png)

5. 完成添加或修改记录之后，请单击**继续**，以查看这些更改。再次选择**继续**，以保存这些更改。

<h2><a name="enabledomain"></a>在您的网站上启用域名</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

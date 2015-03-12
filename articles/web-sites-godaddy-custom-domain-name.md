<properties title="了解如何配置 Azure 网站，以使用借助 GoDaddy 注册的域名" pageTitle="为 Azure 网站配置 GoDaddy 域名" metaKeywords="Azure, Azure  Websites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

# 为 Azure 网站配置自定义域名 (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-cn/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自定义域</a><a href="/zh-cn/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/zh-cn/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/zh-cn/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-cn/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-cn/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-cn/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-cn/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-cn/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-cn/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Websites" class="current">网站</a> | <a href="/zh-cn/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">使用流量管理器的网站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

本文提供了如何将从[Go Daddy](https://godaddy.com)购买的自定义域名用于 Azure 网站的说明。

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文内容：

-   [了解 DNS 记录](#understanding-records)
-   [为自定义域添加 DNS 记录](#bkmk_configurecname)
-   [在网站上启用域](#enabledomain)

<h2><a name="understanding-records"></a>了解 DNS 记录</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]


<h2><a name="bkmk_configurecname"></a>为自定义域添加 DNS 记录</h2>

若要将自定义域与 Azure 网站关联，必须使用 GoDaddy 提供的工具在 DNS 表中为自定义域添加新条目。使用以下步骤找到用于 GoDaddy.com 的 DNS 工具

1. 通过 GoDaddy.com 登录你的帐户，选择**我的帐户**，然后选择**管理我的域**。最后，选择您希望借助您的 Azure 网站使用的域名的下拉列表菜单，然后选择**管理 DNS**。

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. 从**域详细信息**页面，滚动到**DNS 区域文件**选项卡。此部分用于添加和修改用于你的域名的 DNS 记录。 

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

	选择**添加记录**，以添加现有记录。

	到**编辑**现有记录，选择记录旁边的笔和纸张图标。

	> [AZURE.NOTE] 在添加新记录之前，请注意，GoDaddy 已经为常用子域（在编辑器中被称为**主机**）创建了 DNS 记录，如**电子邮件**、**文件**、**邮件**，以及其他。如果你要使用的名称已经存在，请修改现有的记录，而不是创建新记录。

4. 当添加记录时，必须首先选择记录类型。

	![select record type](./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png)

	接下来，您必须提供**主机**（自定义域或子域）和所**指向**的内容。

	![add zone record](./media/web-sites-custom-domain-name/godaddy-addzonerecord.png)

	* 添加**A（主机）记录**时 - 您必须将**主机**字段设置为**@**（表示根域名，如**contoso.com**，） \* （用于匹配多个子域的通配符）或您要使用的子域 （例如，**www**。）必须将**指向**字段设置为你的 Azure 网站的 IP 地址。
	
		> [AZURE.NOTE] 使用 A（主机）记录时，还必须添加带有下列配置的 CNAME 记录：
		> 
		> * **指向** **awverify.&lt;yourwebsitename&gt;.chinacloudsites.cn**值的**awverify**A**主机**值。
		> 
		> 此 CNAME 记录由 Azure 用来验证你拥有 A 记录所描述的域

	* 添加**CNAME（别名）记录**时，必须将**主机**字段设置为你要使用的子域。例如，**www**。必须将**指向**字段设置为你的 Azure 网站的**.chinacloudsites.cn**域名。例如，**contoso.azurwebsites.net**。


5. 完成添加或修改记录之后，请单击**完成**，以保存这些更改。

<h2><a name="enabledomain"></a>启用您的网站上的域名</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]


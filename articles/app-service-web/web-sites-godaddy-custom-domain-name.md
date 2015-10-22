<properties
	pageTitle="在 Azure 网站 (GoDaddy) 中配置自定义域名"
	description="了解如何在 Azure Web Apps 中使用 GoDaddy 提供的域名"
	services="app-service\web"
	documentationCenter=""
	authors="erikre"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.date="09/16/2015"
	wacn.date=""/>

# 为 Azure 网站配置自定义域名 (GoDaddy)

[AZURE.INCLUDE [web-selector](../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

本文提供了如何将从 [Go Daddy](https://godaddy.com) 购买的自定义域名用于 Azure 网站的说明。

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文内容：

-   [了解 DNS 记录](#understanding-records)
-   [为自定义域添加 DNS 记录](#bkmk_configurecname)
-   [在网站上启用域](#enabledomain)

<a name="understanding-records"></a>
## 了解 DNS 记录

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]



<a name="bkmk_configurecname"></a>
## 为自定义域添加 DNS 记录

若要将自定义域与 Azure 网站关联，必须使用 GoDaddy 提供的工具在 DNS 表中为自定义域添加新条目。使用以下步骤找到用于 GoDaddy.com 的 DNS 工具

1. 通过 GoDaddy.com 登录你的帐户，选择“我的帐户”，然后选择“管理我的域”。最后，选择你希望借助你的 Azure Web 应用使用的域名的下拉列表菜单，然后选择“管理 DNS”。

	![GoDaddy 的自定义域页面](./media/web-sites-godaddy-custom-domain-name/godaddy-customdomain.png)

2. 在“域详细信息”页中，滚动到“DNS 区域文件”选项卡。此部分用于添加和修改用于你的域名的 DNS 记录。

	![“DNS 区域文件”选项卡](./media/web-sites-godaddy-custom-domain-name/godaddy-zonetab.png)

	选择“添加记录”，以添加现有记录。

	若要**编辑**现有记录，请选择记录旁边的笔和纸图标。

	> [AZURE.NOTE]在添加新记录之前，请注意，GoDaddy 已经为常用子域（在编辑器中称为“主机”）创建了 DNS 记录，如“电子邮件”、“文件”、“邮件”，以及其他。如果你要使用的名称已经存在，请修改现有的记录，而不是创建新记录。

4. 当添加记录时，必须首先选择记录类型。

	![选择记录类型](./media/web-sites-godaddy-custom-domain-name/godaddy-selectrecordtype.png)

	接下来，你必须提供“主机”（自定义域或子域）和所“指向”内容。

	![添加区域记录](./media/web-sites-godaddy-custom-domain-name/godaddy-addzonerecord.png)

	* 在添加 **A（主机）记录**时，必须将“主机”字段设置为 **@**（代表根域名，如 **contoso.com**）、*（用于匹配多个子域的通配符），或者是你要使用的子域（例如 **www**）。 必须将“指向”字段设置为你的 Azure Web 应用的 IP 地址。
	
		> [AZURE.NOTE]使用 A（主机）记录时，还必须添加带有下列配置的 CNAME 记录：
		> 
		> * **指向** **awverify.&lt;yourwebsitename&gt;.chinacloudsites.cn** 值的 **awverify** 的 **Host** 值。
		> 
		> 此 CNAME 记录由 Azure 用来验证你拥有 A 记录所描述的域

	* 添加 **CNAME（别名）记录**时，必须将“主机”字段设置为你要使用的子域。例如 **www**。必须将“指向”字段设置为你的 Azure 网站的 **.chinacloudsites.cn** 域名。例如 **contoso.azurwebsites.net**。


5. 完成添加或修改记录之后，请单击“完成”，以保存这些更改。

<a name="enabledomain"></a>
## 在 Web 应用上启用域名

[WACOM.INCLUDE [模式](../includes/custom-dns-web-site-enable-on-web-site.md)]

<!---HONumber=74-->
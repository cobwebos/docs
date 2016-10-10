<!-- not suitable for Mooncake -->

<properties
	pageTitle="在 Azure App Service 中配置自定义域名 (GoDaddy)"
	description="了解如何在 Azure Web Apps 中使用 GoDaddy 提供的域名"
	services="app-service"
	documentationCenter=""
	authors="erikre"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/12/2016"
	wacn.date=""
	ms.author="cephalin"/>

# 在 Azure App Service 中配置自定义域名（直接从 GoDaddy 购买的域名）

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro.md)]

如果通过 Azure 应用服务 Web 应用购买了域，请参阅[为 Web 应用购买域](/documentation/articles/custom-dns-web-site-buydomains-web-app/)的最后一个步骤。

本文说明如何为[应用服务 Web 应用](/documentation/articles/app-service-changes-existing-services/)使用直接从 [GoDaddy](https://godaddy.com) 购买的自定义域名。

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## <a name="understanding-records"></a>了解 DNS 记录

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-raw.md)]

## <a name="bkmk_configurecname"></a>为自定义域添加 DNS 记录

若要将自定义域与应用服务中的 Web 应用关联，必须使用 GoDaddy 提供的工具在 DNS 表中为自定义域添加新条目。使用以下步骤找到用于 GoDaddy.com 的 DNS 工具

1. 通过 GoDaddy.com 登录你的帐户，选择“我的帐户”，然后选择“管理我的域”。最后，选择你希望借助你的 Azure Web 应用使用的域名的下拉列表菜单，然后选择“管理 DNS”。

	![GoDaddy 的自定义域页面](./media/web-sites-godaddy-custom-domain-name/godaddy-customdomain.png)

2. 在“域详细信息”页中，滚动到“DNS 区域文件”选项卡。此部分用于添加和修改用于你的域名的 DNS 记录。

	![“DNS 区域文件”选项卡](./media/web-sites-godaddy-custom-domain-name/godaddy-zonetab.png)

	选择“添加记录”，以添加现有记录。

	若要**编辑**现有记录，请选择记录旁边的笔和纸图标。

	> [AZURE.NOTE] 在添加新记录之前，请注意，GoDaddy 已经为常用子域（在编辑器中称为“主机”）创建了 DNS 记录，如“电子邮件”、“文件”、“邮件”，以及其他。如果你要使用的名称已经存在，请修改现有的记录，而不是创建新记录。

4. 当添加记录时，必须首先选择记录类型。

	![选择记录类型](./media/web-sites-godaddy-custom-domain-name/godaddy-selectrecordtype.png)

	接下来，你必须提供“主机”（自定义域或子域）和所“指向”内容。

	![添加区域记录](./media/web-sites-godaddy-custom-domain-name/godaddy-addzonerecord.png)

	* 在添加 **A（主机）记录**时，必须将“主机”字段设置为 **@**（代表根域名，如 **contoso.com**）、*（用于匹配多个子域的通配符），或者要使用的子域（例如 **www**）。 必须将“指向”字段设置为 Azure Web 应用的 IP 地址。*

	* 添加 **CNAME（别名）记录**时，必须将“主机”字段设置为你要使用的子域。例如 **www**。必须将“指向”字段设置为 Azure Web 应用的 **.chinacloudsites.cn** 域名。例如 **contoso.azurwebsites.net**。

5. 单击“添加另一个”。
6. 选择“TXT”作为记录类型，然后指定“主机”值 **@** 和“指向”值 **&lt;yourwebappname&gt;.chinacloudsites.cn**。

	> [AZURE.NOTE] Azure 使用此 TXT 记录来验证用户是否拥有 A 记录或第一条 TXT 记录所描述的域。在 Azure 门户预览版中将域映射到 Web 应用后，可以删除此 TXT 记录条目。

5. 完成添加或修改记录之后，请单击“完成”，以保存这些更改。

## <a name="enabledomain"></a>在 Web 应用上启用域名

[AZURE.INCLUDE [模式](../../includes/custom-dns-web-site-enable-on-web-site.md)]

## 发生的更改
* 有关从网站更改为 App Service 的指南，请参阅 [Azure App Service 及其对现有 Azure 服务的影响](/documentation/articles/app-service-changes-existing-services/)

<!---HONumber=Mooncake_0926_2016-->
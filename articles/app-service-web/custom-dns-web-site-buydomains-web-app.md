
<properties
	pageTitle="如何在 Azure App Service Web Apps 中购买自定义域名"
	description="了解如何在 Azure App Service 中购买 Web 应用的自定义域名。"
	services="app-service\web"
	documentationCenter=""
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.date="05/21/2015"
	wacn.date=""/>

# 在 Azure App Service 中购买和配置自定义域名

<div class="dev-center-tutorial-selector sublanding">
  <a href="/documentation/articles/custom-dns-web-site-buydomains-web-app" title="Web Apps" class="current">为 Web Apps 购买域</a> <a href="/documentation/articles/web-sites-custom-domain-name" title="Web Apps">使用外部域的 Web Apps</a> <a href="/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="使用流量管理器的 Web Apps">使用流量管理器的 Web Apps</a>

</div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

当你创建 Web 应用时，Azure 会将其分配给 azurewebsites.net 的子域。例如，如果你的 Web 应用名为 **contoso**，URL 为 **contoso.azurewebsites.net**。Azure 还会将分配一个虚拟 IP 地址。

对于生产 Web 应用，你可能希望用户看到的自定义域名。本文介绍如何使用 [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 购买和配置自定义域。

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]


## 概述

> [AZURE.NOTE]请不要尝试使用没有与有效信用卡关联的订阅购买域。否则可能导致你的订阅被禁用。

如果你的 Web 应用没有域名，你可以轻松地在 [Azure 管理门户](https://manage.windowsazure.cn)中购买一个。在购买过程中，你可以选择将 WWW 和根域的 DNS 记录自动映射到你的 Web 应用。你也可以直接在 Azure 门户管理域权限。


请使用以下步骤来购买域名，然后将其分配给 Web 应用。

1. 在你的浏览器中，打开 [Azure 管理门户](https://manage.windowsazure.cn)。

2. 在“Web 应用”选项卡中，单击 Web 应用的名称，选择“设置”，然后选择“自定义域和 SSL”。

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

3. 在“自定义域和 SSL”边栏选项卡中，单击“购买域”。

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

4. 在“购买域”边栏选项卡中，使用文本框键入你想要购买的域名，然后按 Enter。将直接在文本框下方显示建议的可用域。选择你想要购买什么域。可以一次性选择购买多个域。

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

5. 单击“联系人信息”，然后填充域的联系人信息表单。

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)

> [AZURE.NOTE]请务必正确填写所有必填字段，尤其是电子邮件地址。如果购买的域没有“隐私保护”，在域生效之前，系统可能要求你验证电子邮件。在某些情况下，如果联系人信息数据不正确，你将无法购买域。

6. 现在你可以选择：

	a) 每年“自动续订”你的域
	
	b) 选择启用购买价格免费涵盖的“隐私保护”
	
	c) 为 WWW 和根域“分配默认主机名”到当前 Web 应用。

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
  
> [AZURE.NOTE]选项 C 将为你自动配置 DNS 绑定和主机名绑定。这样，购买过程一旦完成（除了少数情况下的 DNS 传播延迟），你的 Web 应用就可以访问自定义域。如果 Web 应用在 Azure 流量管理器的后面，你将看不到分配根域的选项，因为 A 记录不能和流量管理器一起使用。
>
>你随时可以通过一个 Web 应用将购买的域/子域分配到另一个 Web 应用，反之亦然。有关详细信息，请参阅步骤 8。

	
7. 单击“购买域”边栏选项卡上的“选择”，然后你就会在“购买确认”边栏选项卡上看到购买信息。如果你接受法律条款，请单击“购买”，然后就会提交你的订单，你可以在“通知”中监视购买流程。购买域的过程可能需要几分钟才能完成。 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)

8. 如果你成功订购了域，则可管理该域并将其分配给你的 Web 应用。单击域右侧的“...”。然后，你可以“取消购买”或“管理域”。单击“管理域”，然后可以在“管理域”边栏选项卡中将子域绑定到 Web 应用。如果你要将一个**子域**绑定到不同的 Web 应用，请从相应 Web 应用的上下文中执行此步骤。在此处，你可以通过从下拉菜单中选择流量管理器名称，将域分配到流量管理器终结点（如果 Web 应用在流量管理器后面）。通过执行此操作，域/子域将自动分配给该流量管理器终结点后面的所有 Web 应用。 

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)

> [AZURE.NOTE]你可以在 5 天内“取消购买”以获取全额退款。5 天后你将无法“取消购买”，而是会看到“删除”域的选项。删除域会在你的订阅中释放该域，并且该域将成为可用的域，在这种情况下不会向你退款。

完成配置后，自定义域名将在 Web 应用的**“主机名绑定”**部分中列出。

此时，你应该可以在浏览器中输入自定义域名，并查看它是否成功地将你转至 Web 应用。
 

<!---HONumber=64-->
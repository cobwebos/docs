<!-- not suitable for Mooncake -->


<properties
	pageTitle="通过 Azure 应用商店创建 Web 应用 | Azure"
	description="了解如何使用 Azure 门户从 Azure 应用商店创建新的 WordPress Web 应用。"
	services="app-service\web"
	documentationCenter=""
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.date="07/11/2016"
	wacn.date=""/>

<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->


# 通过 Azure 应用商店创建 Web 应用

[AZURE.INCLUDE [选项卡](../../includes/app-service-web-get-started-nav-tabs.md)]

Azure 应用商店提供了由 Microsoft、第三方公司和开源软件计划开发的各种流行 Web 应用。例如，WordPress、Umbraco CMS、Drupal 等。这些 Web 应用基于各种常用的框架，例如此 WordPress 示例中的 [PHP]，以及 [.NET]、[Node.js]、[Java]、[Python] 等。若要从 Azure 应用商店创建 Web 应用，你所需要的唯一软件就是用于浏览 [Azure 门户]的浏览器。

在本教程中，你将了解如何：

* 在 Azure 中查找和创建基于 Azure 应用商店模板的 Web 应用。
* 配置适用于新 Web 应用的 Azure 设置。
* 启动和管理 Web 应用。

在本教程中，你将从 Azure 应用商店部署一个 WordPress 博客站点。完成本教程中的步骤后，你将在云中启动并运行自己的 WordPress 站点。

![示例 WordPress Wep 应用仪表板][WordPressDashboard1]  


在本教程中部署的 WordPress 站点使用 MySQL 作为数据库。如果想要改用 SQL 数据库作为数据库，请参阅 [Project Nami]（Nami 项目），Azure 应用商店中也提供了该项目。

> [AZURE.NOTE]
若要完成本教程，你需要一个 Azure 帐户。如果你没有帐户，可以[激活你的 Visual Studio 订户权益][activate]，或者[注册试用帐户][trial]。
>
> 如果你想在注册 Azure 帐户之前就开始使用 Azure，请访问 [Try Azure Web App]（试用 Azure Web 应用）。在那里，你可以立刻在 Azue 中创建短期的入门级 Web 应用 -- 无需信用卡，也无需做出承诺。

## 在 Azure 中查找和创建 Web 应用

1. 登录到 [Azure 门户]。

1. 单击“新建”。
	
	![创建新的 Azure 资源][MarketplaceStart]
	
1. 搜索“WordPress”，然后单击“WordPress”。如果你想使用 SQL 数据库来代替 MySQL，则搜索“Project Nami”。

	![在应用商店中搜索 WordPress][MarketplaceSearch]
	
1. 阅读完 WordPress 应用的说明后，单击“创建”。

	![创建 WordPress Web 应用][MarketplaceCreate]  


## 配置适用于新 Web 应用的 Azure 设置

1. 创建新的 Web 应用后，将显示 WordPress 设置边栏选项卡，你可以使用它来完成以下步骤：

	![配置 WordPress Web 应用设置][ConfigStart]  


1. 在“Web 应用”框中输入 Web 应用的名称。

	该名称在 chinacloudsites.cn 域中必须是唯一的，因为 Web 应用的 URL 将是 *{name}*.chinacloudsites.cn。如果你输入的名称不是唯一的，则会在文本框中显示一个红色的感叹号。

	![配置 WordPress Web 应用名称][ConfigAppName]  


1. 如果你有多个订阅，请选择要使用的那一个。

	![配置 Web 应用的订阅][ConfigSubscription]  


1. 选择“资源组”或新建一个。

	有关资源组的详细信息，请参阅 [Azure Resource Manager 概述][ResourceGroups]。

	![配置 Web 应用的资源组][ConfigResourceGroup]

1. 选择“App Service 计划/位置”或新建一个。

	有关 App Service 计划的详细信息，请参阅 [Azure App Service 计划概述][AzureAppServicePlans]。

	![配置 Web 应用的服务计划][ConfigServicePlan]  


1. 单击“数据库”，然后在“新建 MySQL 数据库”边栏选项卡中提供配置 MySQL 数据库所需的值。

	a.输入新名称或保留默认名称。

	b.保留“数据库类型”设置，即设置为“共享”。

	c.选择的位置与你为 Web 应用选择的位置一样。

	d.选择一个定价层。**Mercury** - 免费提供最少数量的连接和最少容量的磁盘空间 - 对于本教程已足够。

	e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。

	![配置 Web 应用的数据库设置][ConfigDatabase]  


1. 在“WordPress”边栏选项卡中，接受法律条款，然后单击“创建”。

	![完成 Web 应用设置并单击“确定”][ConfigFinished]  


	Azure 通常会在不到一分钟内创建好 Web 应用。你可以单击门户页顶部的铃铛图标来观看进度。

	![进度指示器][ConfigProgress]

## 启动和管理你的 WordPress Web 应用
	
1. 创建完 Web 应用以后，请在 Azure 门户中导航到你在其中创建了应用程序的资源组，你可以看到 Web 应用和数据库。

	带灯泡图标的额外资源是 [Application Insights][ApplicationInsights]，提供适用于 Web 应用的监视服务。

1. 在“资源组”边栏选项卡中，单击 Web 应用行。

	![选择 WordPress Web 应用][WordPressSelect]  


1. 在“Web 应用”边栏选项卡中，单击“浏览”。

	![浏览到你的 WordPress Web 应用][WordPressBrowse]

1. 如果系统提示你选择 WordPress 博客的语言，请选择所需的语言，然后单击“继续”。

	![配置 WordPress Web 应用的语言][WordPressLanguage]  


1. 在 WordPress 的“欢迎”页中，输入 WordPress 所需的配置信息，然后单击“安装 WordPress”。

	![配置 WordPress Web 应用的设置][WordPressConfigure]  


1. 使用你在“欢迎”页中创建的凭据登录。

1. 随后将打开站点的“仪表板”页，其中显示了你提供的信息。

	![查看 WordPress 仪表板][WordPressDashboard2]  


## 后续步骤

在本教程中，你已了解如何从 Azure 应用商店创建和部署一个示例 Web 应用。

有关如何使用 Azure Web Apps 的详细信息，请参阅页面左侧（针对宽屏浏览器窗口）或页面顶部（针对窄屏浏览器窗口）的链接。

有关在 Azure 上开发 WordPress Web 应用的详细信息，请参阅[在 Azure Web 应用中开发 WordPress][WordPressOnAzure]。

<!-- URL List -->


[PHP]: /develop/php/
[.NET]: /develop/net/
[Node.js]: /develop/nodejs/
[Java]: /develop/java/
[Python]: /develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[trial]: /pricing/1rmb-trial/
[Try Azure Web App]: https://tryappservice.azure.com/
[ResourceGroups]: /documentation/articles/resource-group-overview/
[AzureAppServicePlans]: /documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Azure 门户]: https://portal.azure.cn/
[Project Nami]: http://projectnami.org/
[WordPressOnAzure]: /documentation/articles/develop-wordpress-on-app-service-web-apps/

<!-- IMG List -->


[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png

<!----HONumber=Mooncake_0815_2016-->
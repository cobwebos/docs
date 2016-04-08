<properties
	pageTitle="Web Apps 概述"
	description="了解有关 App Service Web Apps 的详细信息"
	services="app-service\web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.date="03/24/2015"
	wacn.date=""/>


#Web Apps 概述

[App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 是一个完全托管平台，专业开发人员可通过它在 Web、移动和集成方案中实现一系列丰富功能。通过使用 Azure App Service，快速创建和部署可与你的业务一起缩放的任务关键 Web 应用。

利用 [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 的功能使用你熟悉和依赖的语言和框架，将你的应用程序快速部署到 Azure 云，并持续改进你的代码而无需再考虑基础结构。

![Web 应用商店](./media/app-service-web-overview/marketplace.png)

## 不只是网站##

现代企业用与客户交互的方式越来越复杂。所有类型的公司都将公司 Web 平台视为其业务的关键内容、业务计划的重要组成部分。为了适应这种重要性，各企业正在寻找一个可提供灵活性、安全性和可伸缩性的平台。此外，他们还要求能够链接到其现有的业务系统，能够快速部署新代码以及在全球范围内扩展实例。凭借 Azure App Service 和 Web Apps，组织可以快速又经济地满足客户。

## 为什么选择 Web Apps？ ##

Azure App Service Web Apps 是一个完全托管平台，凭借这一平台，你可以在数秒内构建、部署并缩放企业级 Web 应用。只需专注于应用程序代码，放手让 Azure 处理基础结构，为你进行缩放和安全运行。Web Apps 的特点：

- **用户熟悉，使用快速** - 运用现有技能，使用喜爱的语言、框架和 IDE 进行编码。只需几次单击，即可向你现有的 Web 应用添加版本控制、更新、单一登录、标识代理、独立存储和性能监视功能。访问内容丰富的库，将其用作构建基块以加快开发速度。使用持续集成、活动网站调试和业界领先的 Visual Studio IDE 等前沿功能，体验无与伦比的开发人员工作效率。
- **企业级** - Web Apps 设计用于生成和托管安全的任务关键应用程序。生成安全连接到本地资源的 Active Directory 集成业务应用，在符合 ISO、SOC2 和 PCI 的安全云平台上进行托管。更能同时享受企业级 SLA。
- **全球缩放** - Web Apps 已实现优化，可在全球数据中心基础结构上提供可用性和自动缩放。根据需要轻松缩放应用程序。可在各个地理区域内和跨地理区域间提供高可用性。在多个位置复制数据和托管服务更加方便快捷，点击鼠标即可扩展到新区域和地理位置。  

## Web Apps 概念 ##

- **Web Apps 库** - 从不断增长的现有 Web 应用程序模板列表中选择。一键安装 Wordpress、Joomla 和 Drupal 等程序包，充分利用 OSS 应用社区。通过利用 .NET MVC、Django 和 CakePHP 等框架，立即开始应用程序开发过程。
- **自动缩放** - 凭借 Web Apps，可以快速缩放以处理任何入站客户负载。手动选择 VM 的数量和大小，或设置自动缩放以基于负载或计划来缩放服务器。
- **持续集成** - 使用 VSO、GitHub、TeamCity、Hudson 或 BitBucket 设置持续集成和部署工作流，从而可以针对每个成功的代码签入或集成测试来自动生成、测试和部署 Web 应用。
- **部署槽** - 实现[预留部署][Slots]来验证预生产环境中的代码，这与 Azure App Service 中的生产 Web 应用相同。满意后，即可通过执行交换操作，在零停机时间的情况下发布应用的新版本。 
- **生产中测试** - 升级预留部署并执行 A/B 测试，从而用实时流量的可配置部分来验证新代码。 
- **Webjobs** - 在 Web Apps VM 上运行任何程序或脚本。持续或按计划运行作业，进行扩展以在多台 VM 上运行。使用 Azure [WebJobs SDK][Webjobs] 与 Azure 存储空间或服务总线集成。
- **混合连接** - 使用[混合连接](/documentation/articles/integration-hybrid-connection-overview)和[虚拟网络](/documentation/articles/web-sites-integrate-with-vnet)访问本地数据。

## 入门 ##
要开始使用 Web Apps，请按照[创建 ASP.NET Web 应用][create]教程进行操作。

有关 Azure App Service 平台的详细信息，请参阅 [Azure App Service][appservice]。

>[AZURE.NOTE]如果你想要在注册 Azure 帐户之前开始使用 Azure App Service，请转到[试用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，你可以通过该网站在 App Service 中立即创建一个生存期较短的入门 Web 应用。你不需要使用信用卡，也不需要做出承诺。

## 发生的更改
* 有关从网站更改为 App Service 的指南，请参阅：[Azure App Service 及其对现有 Azure 服务的影响](http://go.microsoft.com/fwlink/?LinkId=529714)
* 有关从旧门户更改为新门户的指南，请参阅：[有关在预览门户中导航的参考](http://go.microsoft.com/fwlink/?LinkId=529715)

[appservice]: /documentation/articles/app-service-value-prop-what-is
[create]: /documentation/articles/web-sites-dotnet-get-started
[Webjobs]: /documentation/articles/websites-dotnet-webjobs-sdk-get-started
[Slots]: /documentation/articles/web-sites-staged-publishing

 

<!----HONumber=67-->
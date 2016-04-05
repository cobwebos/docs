<!-- not suitable for Mooncake -->

<properties
   pageTitle="测试 Azure Web 应用的性能 | Azure"
   description="运行 Azure Web 应用性能测试以检查你的应用如何处理用户负载。测量响应时间，并查找可能表明出现问题的失败状况。"
   services="app-service\web"
   documentationCenter=""
   authors="ecfan"
   manager="douge"
   editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.date="01/13/2016"
	wacn.date=""/>

# 测试 Azure Web 应用在低负载下的性能

在推出 Web 应用或将更新部署到生产环境之前，先检查该应用的性能。这样，便可更妥善地评估应用是否已准备好发布。对应用更具信心，其可在尖峰使用期间或在下一波推式营销时处理流量。

在公开预览期，你可以在 Azure 管理门户中免费测试应用的性能。这些测试将模拟应用在特定期间内的用户负载并测量应用的响应度。例如，测试结果显示应用对指定数量的用户的响应速度。它们还会显示有多少个请求失败（这可能表示应用有问题）。

![在 Web 应用中查找性能问题][TestOverview]

## 开始之前

*	你需要一个 [Azure 订阅][AzureSubscription]（如果还没有的话）。了解如何[免费注册 Azure 帐户][AzureFreeTrial]。

*	需要一个 [Visual Studio Team Services (Team Services)][WhatIsTeamServices] 帐户才能保留性能测试历史记录。请在设置性能测试时创建新帐户，如果你是帐户所有者，请使用现有帐户。[Visual Studio Team Service 帐户还有什么用途？](#TeamServicesAccount)

*	部署应用以便在非生产环境中进行测试。让应用使用生产环境中所用方案以外的 App Service 计划。这样就不会影响任何现有的客户或让应用在生产环境中变慢。

## 设置和运行性能测试

0.	登录到 [Azure 管理门户][AzurePortal]。若要使用你拥有的 Visual Studio Team Services 帐户，请以帐户所有者的身份登录。

0.	转到你的 Web 应用。

	![转到“全部浏览”、“Web Apps”、你的 Web 应用][WebApp]

0.	转到“性能测试”。

	![转到“工具”、“性能测试”][ExpandedTools]
 
0.	现在请链接 [Visual Studio Team Services (Team Services)][WhatIsTeamServices] 帐户以保留性能测试历史记录。

	如果你已有 Team Services 帐户，请选择该帐户。如果没有，请创建新帐户。

	![选择现有的 Team Services 帐户，或创建新的帐户][ExistingNewTeamServicesAccount]

0.	创建性能测试。设置详细信息并运行测试。可以一边运行测试，一边实时查看结果。

	例如，假设我们有在去年节日特卖时发放优惠券的应用。此活动持续了 15 分钟，高峰负载为 100 位并发客户。我们希望今年的客户数变成两倍。我们还想让页面加载时间从 5 秒降低为 2 秒，以改善客户满意度。因此，我们将以 250 名用户测试更新后应用的性能达 15 分钟之久。

	我们生成同时访问我们的网站的虚拟用户（客户），以此模拟应用的负载。这显示有多少个请求失败或响应速度较慢。

	![创建、设置并运行性能测试][NewTest]

	 *	系统自动添加 Web 应用的默认 URL。可以更改此 URL 以测试其他页面（仅限 HTTP GET 请求）。

	 *	若要模拟本地情况并降低延迟，请选择最靠近用户的位置来生成负载。

	以下是正在进行的测试。在第一分钟内，我们的页面加载速度低于预期。

	![正在进行的包含实时数据的性能测试][TestRunning]

	测试完成后，我们了解页面加载速度在第一分钟后变快许多。这有助于找出我们要开始排查问题的位置。

	![已完成的性能测试将显示结果，包括失败的请求][TestDone]
	
欢迎各位提供反馈。如有疑问或问题，请联系我们：<vsoloadtest@microsoft.com>

##	问题解答

#### 问：持续运行测试的时间是否有限制？ 

答：是，最多可以在 Azure 管理门户中运行一小时的测试。

#### 问：可运行性能测试的时间有多久？ 

答：公共预览版发布后，通过 Visual Studio Team Services 帐户每个月可免费获取 20,000 虚拟用户分钟数 (VUM)。VUM 是虚拟用户数乘以测试中的分钟数。如果要求超过免费限制，可以购买更多时间，你只需支付所用的时间。

#### 问：哪里可以检查到目前为止我已使用多少 VUM？

答：可以在 Azure 管理门户中检查此数量。

![转到你的 Team Services 帐户][TeamServicesAccount]

![检查已用的 VUM][CheckTestTime]

<a name="VSOAccount"></a>
#### 问：Visual Studio Team Service 帐户还有什么用途？

答：若要查找新帐户，请转到 ```https://{accountname}.visualstudio.com```。使用任何工具或语言来共享代码、构建、测试、跟踪工作和交付软件 – 一切尽在云中进行。详细了解 [Visual Studio Team Services][WhatIsTeamServices] 功能与服务如何帮助你的团队更轻松地协作以及持续进行部署。

<!--Image references-->
[WebApp]: ./media/app-service-web-app-performance-test/azure-np-web-apps.png
[TestOverview]: ./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png
[ExpandedTools]: ./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png
[ExistingNewTeamServicesAccount]: ./media/app-service-web-app-performance-test/azure-np-no-vso-account.png
[NewTest]: ./media/app-service-web-app-performance-test/azure-np-new-performance-test.png
[TestRunning]: ./media/app-service-web-app-performance-test/azure-np-running-perf-test.png
[TestDone]: ./media/app-service-web-app-performance-test/azure-np-perf-test-done.png
[TeamServicesAccount]: ./media/app-service-web-app-performance-test/azure-np-vso-accounts.png
[CheckTestTime]: ./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png

<!--Reference links -->
[AzurePortal]: https://manage.windowsazure.cn
[AzureSubscription]: https://account.windowsazure.cn/subscriptions
[AzureFreeTrial]: /pricing/1rmb-trial/?WT.mc_id=A261C142F
[WhatIsTeamServices]: https://www.visualstudio.com/products/what-is-visual-studio-online-vs

<!---HONumber=Mooncake_0328_2016-->
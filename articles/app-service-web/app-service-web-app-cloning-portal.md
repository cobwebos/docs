<!-- not suitable for Mooncake -->

<properties
	pageTitle="使用 Azure 管理门户克隆 Web 应用"
	description="了解如何使用 Azure 管理门户将 Web 应用克隆到新的 Web 应用。"
	services="app-service\web"
	documentationCenter=""
	authors="ahmedelnably"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.date="03/08/2016"
	wacn.date=""/>

# 使用 Azure 管理门户克隆 Azure Web 应用#

[Azure Web Apps](/documentation/services/web-sites/) 中的克隆功能可让你轻松将现有 Web 应用克隆到位于不同或相同区域中的新建应用。这样，客户就可以跨不同区域部署许多应用。

应用克隆目前仅支持高级层 App Service 计划。新功能使用与 Web Apps 备份功能相同的限制，具体请参阅[在 Azure 中备份 Web 应用](/documentation/articles/web-sites-backup)。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../includes/app-service-web-to-api-and-mobile.md)]


## 克隆现有应用 ##

Web 应用必须在“高级”模式下运行，才能为 Web 应用创建副本。

1. 在 [Azure 管理门户](https://manage.windowsazure.cn/)中，打开 Web 应用的边栏选项卡。
2. 单击“工具”。然后，在“工具”边栏选项卡中，单击“克隆应用”。

	![][1]

	> [AZURE.NOTE]
	如果 Web 应用不是处于“高级”模式，你将收到消息，指出应用克隆支持的模式。此时，你可以选择“升级”。
	
3. “克隆应用”边栏选项卡中提供了新 Web 应用、资源组和 App Service 计划的名称。用户也可以选择是否克隆多个源 Web 应用设置。

	![][2]

4. 单击“创建”后，平台将开始创建源 Web 应用的副本。

## 将现有应用克隆到 Azure 环境##

在“克隆应用”边栏选项卡中，客户可以选择现有 Azure 环境中的应用池。

## 当前限制 ##

此功能目前以预览版提供，我们正在不断地添加新功能。以下是当前在 Azure 管理门户中支持应用克隆存在的已知限制：

- 不会克隆 Azure 流量管理器设置
- 不会克隆自动缩放设置
- 不会克隆备份计划设置
- 不会克隆 VNET 设置
- 不会自动在目标 Web 应用上设置 App Insights
- 不会克隆简易身份验证设置
- 不会克隆 Kudu 扩展
- 不会克隆 TiP 规则


### 参考 ###
- [使用 PowerShell 克隆 Web 应用](/documentation/articles/app-service-web-app-cloning)
- [在 Azure 中备份 Web 应用](/documentation/articles/web-sites-backup)
- [如何创建 Azure 环境](/documentation/articles/app-service-web-how-to-create-an-app-service-environment)
- [在 Azure 环境中创建 Web 应用](/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase)
- [Azure 环境简介](/documentation/articles/app-service-app-service-environment-intro)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png
<!---HONumber=Mooncake_0328_2016-->
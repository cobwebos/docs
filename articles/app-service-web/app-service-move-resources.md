<properties
	pageTitle="将 Web 应用资源移到另一个资源组"
	description="说明将 Web Apps 和 App Services 从一个资源组移到另一个资源组的方案。"
	services="app-service\web"
	documentationCenter=""
	authors="ZainRizvi"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.date="07/28/2015"
	wacn.date=""/>
	
# 受支持的迁移配置

你可以使用 [ARM 迁移资源 API](/documentation/articles/resource-group-move-resources) 迁移 Azure Web 应用资源（Web Apps 和 App Services）。

Azure Web Apps 当前支持以下迁移方案：

* 将某个资源组的全部内容移到另一个资源组
	* 请注意：在此方案中，目标资源组不能包含任何 Microsoft.Web 资源
* 将单独 Web 应用移到不同的资源组，同时仍然在其当前应用服务中托管这些 Web 应用（该应用服务保留在旧资源组中）

<!---HONumber=67-->
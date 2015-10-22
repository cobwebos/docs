<properties 
	 pageTitle="如何对 Azure 启用内容交付网络 (CDN)" 
	 description="本主题介绍如何对 Azure 启用内容交付网络 (CDN)。" 
	 services="cdn" 
	 documentationCenter="" 
	 authors="zhangmanling" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.date="09/01/2015" 
	 wacn.date=""/>



#如何对 Azure 启用内容交付网络 (CDN)  

可以通过 Azure 管理门户为源启用 CDN。当前可用的源类型包括：Web Apps、存储、云服务。你还可以为 Azure 媒体服务流式处理终结点启用 CDN。一旦为源启用 CDN 终结点，所有公开可用的对象都可进行 CDN 边缘缓存。

请注意，你现在还可以创建自定义源，该源不必是 Azure。

##创建新的 CDN 终结点的步骤  

1.	登录到 [Azure 管理门户](http://manage.windowsazure.cn/)。
2.	在导航窗格中，单击“CDN”。
3.	在功能区上，单击“新建”。在“新建”对话框中，选择“应用服务”，然后选择“CDN”，再选择“快速创建”。
4.	在“源类型”下拉列表中，从一系列可用源类型中选择一个源类型。
	
	可用源 URL 的列表将显示在“源 URL”下拉列表中。
	

	![createnew][createnew]

	如果你选择“自定义源”，则可以输入自定义的源 URL。该源不必是 Azure 源。

	![customorigin][customorigin]

	>[AZURE.NOTE]目前仅支持将 HTTP 用于源，你必须使用媒体服务扩展才能为 Azure 媒体服务流式处理终结点启用 Azure CDN。
	
5.	单击“创建”按钮创建新的终结点。


>[AZURE.NOTE]为终结点创建的配置将不能立即可用；最多需要 60 分钟时间进行注册以便通过 CDN 网络传播。尝试立即使用 CDN 域名的用户可能会收到状态代码 400（错误请求），直到可通过 CDN 访问该内容。

##另请参阅
[如何将内容交付网络 (CDN) 内容映射到自定义域](/documentation/articles/cdn-map-content-to-custom-domain)

[createnew]: ./media/cdn-create-new-endpoint/cdn-create-new-account.png

[customorigin]: ./media/cdn-create-new-endpoint/cdn-custom-origin.png
 

<!---HONumber=74-->
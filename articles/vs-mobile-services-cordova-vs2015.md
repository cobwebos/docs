<properties title="Using Mobile Services with Cordova Projects" pageTitle="" metaKeywords="Azure, Cordova, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/11/2014" ms.author="ghogen" />

### 在 Visual Studio 2015 预览版的 Cordova 项目中使用移动服务

若要在 Visual Studio 2015 预览版的 Cordova 项目中使用 Azure 移动服务，你需要应用以下解决方法。

1. 在 Visual Studio 2015 预览版的 Cordova 项目中，打开 Config.xml，并在**"插件"**选项卡下，启用**"Microsoft Azure 移动服务"**插件。<br/>
![][1]

2. 在 index.html 中，删除引用**MobileServices.Web-1.2.2.min.js**的行。<br/>

<PRE style="prettyprint">
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>MyCordovaApp>/title>

    <!--MyCordovaApp references -->
    <link href="css/index.css" rel="stylesheet" />
</head>
<body>
    <p>Hello world!&lt/p>

    <!--Cordova reference, this is added to your app when it's build. -->
    <script src="cordova.js"></script>
    <script src="scripts/platformOverrides.js"></script>

    <script src="scripts/index.js"></script>

    <!-- yourservicename references -->
    <span style="background-color:yellow"><script src="//ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.2.min.js">&lt/script></span>
    <script src="/services/mobileservices/settings/yourservicename.js"></script>
</body>
</html>
</PRE>

3. 在 services -> mobileServices -> settings 文件夹下打开 {yourservicename}.js，使用以下内容替换现有代码段：

		// http://go.microsoft.com/fwlink/?LinkID=290993&clcid=0x409
		var {yourservicename}Client;
		document.addEventListener("deviceready", function () {    
            mhadetestserviceClient = new WindowsAzure.MobileServiceClient(
                    "https://{yourservicename}.azure-mobile.net/",
                    "<your application key>");
		});

[1]: ./media/vs-mobile-services-cordova-vs2015/AzureMobileServicesPlugIn.png 

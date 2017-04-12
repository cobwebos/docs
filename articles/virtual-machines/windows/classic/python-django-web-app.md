---
title: "在 Azure VM 上构建 Django 应用 | Microsoft Docs"
description: "本教程演示如何使用经典部署模型在 Azure 中使用 Windows Server 2012 R2 Datacenter 虚拟机托管基于 Django 的网站。"
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-service-management
ms.assetid: e36484d1-afbf-47f5-b755-5e65397dc1c3
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 08/04/2015
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d777d2a7944d17a452732c0e820dc781357bc8d2
ms.lasthandoff: 04/03/2017


---
# <a name="django-hello-world-web-application-on-a-windows-server-vm"></a>Windows Server VM 上的 Django Hello World Web 应用程序
> [!div class="op_single_selector"]
> * [Windows](python-django-web-app.md)
> * [Mac/Linux](../../linux/python-django-web-app.md)
> 
> 

<br>

> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。 有关用于部署 Django 的 Resource Manager 模板，请参阅[此处](https://azure.microsoft.com/documentation/templates/django-app/)。

本教程介绍如何在 Microsoft Azure 中使用 Windows Server 虚拟机托管基于 Django 的网站。 本教程假定你之前未使用过 Azure。 完成本教程之后，你将能够在云中启动和运行基于 Django 的应用程序。

你将了解如何执行以下操作：

* 设置 Azure 虚拟机以托管 Django。 虽然本教程介绍如何在 Windows Server 下实现此目的，但也可以使用托管在 Azure 中的 Linux VM 实现相同目的。
* 从 Windows 创建新的 Django 应用程序。

通过按照本教程中的说明进行操作，你将构建一个简单的 Hello World Web 应用程序。 该应用程序将托管在 Azure 虚拟机中。

接下来显示的是已完成应用程序的屏幕截图。

![显示 Azure 上的 hello world 页面的浏览器窗口][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>创建并配置 Azure 虚拟机以托管 Django
1. 按照[此处](tutorial.md)提供的说明创建 Windows Server 2012 R2 Datacenter 分发的 Azure 虚拟机。
2. 指示 Azure 将来自 Web 的端口 80 通信定向到虚拟机上的端口 80：
   
   * 在 Azure 经典门户中导航到新创建的虚拟机，然后单击“终结点”选项卡。
   * 单击屏幕底部的“添加”按钮。
     ![添加终结点](./media/python-django-web-app/django-helloworld-addendpoint.png)
   * 打开 **TCP** 协议的**公用端口 80** 作为**专用端口 80**。
     ![][port80]
3. 在“仪表板”选项卡上，单击“连接”以使用**远程桌面**远程登录到新创建的 Azure 虚拟机。  

**重要说明：**下面的所有说明假定已正确登录到虚拟机，并且正在从虚拟机而非本地计算机发布命令。

## <a id="setup"> </a>安装 Python、Django、WFastCGI
**注意：**若要使用 Internet Explorer 下载，可能需要配置 IE ESC 设置（依次单击“开始”/“管理工具”/“服务器管理器”/“本地服务器”，然后单击“IE 增强的安全配置”，将其设置为“关闭”）。

1. 从 [python.org][python.org] 安装最新的 Python 2.7 或 3.4。
2. 使用 pip 安装 wfastcgi 和 django 包。
   
    对于 Python 2.7，使用以下命令。
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    对于 Python 3.4，使用以下命令。
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="installing-iis-with-fastcgi"></a>安装具有 FastCGI 的 IIS
1. 安装具有 FastCGI 支持的 IIS。  执行此操作可能需要几分钟。
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="creating-a-new-django-application"></a>创建新的 Django 应用程序
1. 从 *C:\inetpub\wwwroot* 输入以下命令以创建新的 Django 项目：
   
   对于 Python 2.7，使用以下命令。
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   对于 Python 3.4，使用以下命令。
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![New-AzureService 命令的结果](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. **django-admin** 命令可生成基于 Django 的网站的基本结构：
   
   * **helloworld/manage.py** 可帮助用户启动托管和停止托管基于 Django 的网站
   * **helloworld\helloworld\settings.py** 包含应用程序的 Django 设置。
   * **helloworld\helloworld\urls.py** 包含每个 url 及其视图之间的映射代码。
3. 在 *C:\inetpub\wwwroot\helloworld\helloworld* 目录中创建一个名为 **views.py** 的新文件。 这会包含呈现“hello world”页面的视图。 启动编辑器并输入以下代码：
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. 将 urls.py 文件的内容替换为以下代码。
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="configuring-iis"></a>配置 IIS
1. 解锁全局 applicationhost.config 中的处理程序节。  这将在 web.config 中启用 python 处理程序。
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. 启用 WFastCGI。  这会将应用程序添加到引用 Python 解释程序可执行文件和 wfastcgi.py 脚本的全局 applicationhost.config。
   
    Python 2.7：
   
        c:\python27\scripts\wfastcgi-enable
   
    Python 3.4：
   
        c:\python34\scripts\wfastcgi-enable
3. 在 *C:\inetpub\wwwroot\helloworld* 中创建 web.config 文件。  `scriptProcessor` 属性的值应与上一步的输出相匹配。  有关 wfastcgi 设置的详细信息，请参阅 pypi 上 [wfastcgi][wfastcgi] 的相关页。
   
    Python 2.7：
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
   
    Python 3.4：
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
4. 更新 IIS 默认网站的位置以指向 django 项目文件夹。
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. 最后，在你的浏览器中加载网页。

![显示 Azure 上的 hello world 页面的浏览器窗口][1]

## <a name="shutting-down-your-azure-virtual-machine"></a>关闭你的 Azure 虚拟机
完成本教程后，请关闭并/或删除你新创建的 Azure 虚拟机以为其他教程释放资源并避免产生 Azure 使用费。

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi


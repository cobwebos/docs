---
title: Windows Server Azure VM 上的 Django Web 应用 | Microsoft Docs
description: 了解如何使用经典部署模型在 Azure 中使用 Windows Server 2012 R2 Datacenter VM 托管基于 Django 的网站。
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: ''
tags: azure-service-management
ms.assetid: e36484d1-afbf-47f5-b755-5e65397dc1c3
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 05/31/2017
ms.author: huvalo
ms.openlocfilehash: 01fa162d41e03e29f3b6f0ca128e7cc49aa91abb
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2017
ms.locfileid: "24057012"
---
# <a name="django-hello-world-web-app-on-a-windows-server-vm"></a>Windows Server VM 上的 Django Hello World Web 应用

> [!IMPORTANT] 
> Azure 具有用于创建和处理资源的两个不同的部署模型：[Azure 资源管理器和经典部署模型](../../../resource-manager-deployment-model.md)。 本文介绍经典部署模型。 我们建议在大多数新部署中使用 Resource Manager 模型。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

本教程演示如何在 Azure 虚拟机的 Windows Server 中托管基于 Django 的网站。 本教程假定你以前没有使用过 Azure。 完成本教程之后，将能够在云中启用和运行基于 Django 的应用程序。

了解如何：

* 设置 Azure 虚拟机以托管 Django。 虽然本教程介绍如何在 Windows Server 中执行此操作，但也可在 Azure 托管的 Linux VM 中执行相同操作。
* 在 Windows 中创建新的 Django 应用程序。

本教程演示如何生成一个基础 Hello World Web 应用程序。 该应用程序托管在 Azure 虚拟机中。

以下屏幕截图显示已完成的应用程序：

![显示 Azure 中的 hello world 页面的浏览器窗口][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>创建并设置 Azure 虚拟机以托管 Django

1. 若要使用 Windows Server 2012 R2 Datacenter 分发创建 Azure 虚拟机，请参阅[在 Azure 门户中创建运行 Windows 的虚拟机](tutorial.md)。
2. 设置 Azure 以将来自 Web 的端口 80 通信定向到虚拟机上的端口 80：
   
   1. 在 Azure 门户中，转到仪表板并选择新创建的虚拟机。
   2. 单击“终结点”，并单击“添加”。

     ![添加终结点](./media/python-django-web-app/django-helloworld-add-endpoint-new-portal.png)

   3. 在“添加终结点”页上，针对“名称”，输入“HTTP”。 将公共和专用 TCP 端口设置为“80”。

     ![输入名称并设置公共和专用端口](./media/python-django-web-app/django-helloworld-add-endpoint-set-ports-new-portal.png)

   4. 单击 **“确定”**。
     
3. 在仪表板中，选择你的 VM。 要使用远程桌面协议 (RDP) 远程登录到新创建的 Azure 虚拟机，请单击“连接”。  

> [!IMPORTANT] 
> 以下说明假定你已正确登录到虚拟机。 同时还假定你正在虚拟机（而非本地计算机）中发出命令。

## <a id="setup"> </a>安装 Python、Django 和 WFastCGI
> [!NOTE]
> 若要使用 Internet Explorer 下载，可能需要配置 Internet Explorer“增强的安全配置”设置。 为此，请单击“开始” > “管理工具” > “服务器管理器” > “本地服务器”。 单击“IE 增强的安全配置”，然后选择“关闭”。

1. 从 [python.org][python.org] 安装最新版本的 Python 2.7 或 Python 3.4。
2. 使用 pip 安装 wfastcgi 和 django 包。
   
    对于 Python 2.7，使用以下命令：
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    对于 Python 3.4，使用以下命令：
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="install-iis-with-fastcgi"></a>安装具有 FastCGI 的 IIS
* 安装具有 FastCGI 支持的 Internet Information Services (IIS)。 执行此操作可能需要几分钟。
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="create-a-new-django-application"></a>创建新的 Django 应用程序
1. 在 C:\inetpub\wwwroot 中，输入以下命令以创建新的 Django 项目：
   
   对于 Python 2.7，使用以下命令：
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   对于 Python 3.4，使用以下命令：
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![New-AzureService 命令的结果](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. `django-admin` 命令生成基于 Django 的网站的基本结构：
   
   * `helloworld\manage.py` 帮助你开始托管和停止托管你的基于 Django 的网站。
   * `helloworld\helloworld\settings.py` 包含应用程序的 Django 设置。
   * `helloworld\helloworld\urls.py` 包含每个 URL 及其视图之间的映射代码。
3. 在 C:\inetpub\wwwroot\helloworld\helloworld 目录中创建一个名为 views.py 的新文件。 此文件包含用于呈现“hello world”页面的视图。 在代码编辑器中输入以下命令：
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. 将 urls.py 文件的内容替换为以下命令：
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-iis"></a>设置 IIS
1. 在全局 applicationhost.config 文件中，解锁处理程序节。  这样，你的 web.config 文件便可使用 Python 处理程序。 添加以下命令：
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. 激活 WFastCGI。 这会将应用程序添加到引用 Python 解释程序可执行文件和 wfastcgi.py 脚本的全局 applicationhost.config 文件。
   
    在 Python 2.7 中：
   
        C:\python27\scripts\wfastcgi-enable
   
    在 Python 3.4 中：
   
        C:\python34\scripts\wfastcgi-enable
3. 在 C:\inetpub\wwwroot\helloworld 中创建 web.config 文件。 `scriptProcessor` 属性的值应与上一步的输出相匹配。 有关 wfastcgi 设置的详细信息，请参阅 [pypi wfastcgi][wfastcgi]。
   
   在 Python 2.7 中：
   
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
   
   在 Python 3.4 中：
   
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
4. 更新 IIS 默认网站的位置以指向 django 项目文件夹：
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. 在浏览器中加载网页。

![显示 Azure 的 hello world 页面的浏览器窗口][1]

## <a name="shut-down-your-azure-virtual-machine"></a>关闭 Azure 虚拟机
完成本教程后，建议关闭或删除为本教程创建的 Azure VM。 这将为其他教程释放资源，同时可以避免产生 Azure 使用费用。

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi

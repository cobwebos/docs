---
title: "在 Linux 上使用 Django 的 Python Web 应用 | Microsoft Docs"
description: "了解如何在 Azure 中使用 Linux 虚拟机托管基于 Django 的 Web 应用程序。"
services: virtual-machines-linux
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-resource-manager
ms.assetid: 00ad4c2c-4316-4f9a-913f-f7f49b158db7
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: python
ms.topic: article
ms.date: 11/17/2015
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 48644685a296875b8884cd4357f49391e30f034a


---
# <a name="django-hello-world-web-application-on-a-linux-vm"></a>Linux VM 上的 Django Hello World Web 应用程序
> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-classic-python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Mac/Linux](virtual-machines-linux-python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

<br>

本教程介绍如何在 Microsoft Azure 中使用 Linux 虚拟机托管基于 Django 的网站。 本教程假定你之前未使用过 Azure。 完成本指南之后，你将能够在云中启动和运行基于 Django 的应用程序。

你将了解如何执行以下操作：

* 设置 Azure 虚拟机以托管 Django。 虽然本教程介绍如何在 **Linux** 下实现此目的，但也可以使用托管在 Azure 中的 Windows Server VM 实现相同目的。 
* 从 Linux 创建新的 Django 应用程序。

通过按照本教程中的说明进行操作，您将构建一个简单的 Hello World Web 应用程序。 该应用程序将托管在 Azure 虚拟机中。

以下是已完成应用程序的屏幕快照：

![显示 Azure 上的 hello world 页面的浏览器窗口](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)

[!INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>创建并配置 Azure 虚拟机以托管 Django
1. 按照[此处](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)提供的说明可创建 *Ubuntu Server 14.04 LTS* 分发的 Azure 虚拟机。  如果需要，可以选择密码身份验证而不是 SSH 公钥。
2. 使用[此处](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)的说明编辑网络安全组，以允许将 http 流量传入到端口 80。
3. 默认情况下，新的虚拟机没有完全限定域名 (FQDN)。  可以按照[此处](virtual-machines-linux-portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)的说明创建一个。  完成本教程时，此步骤是可选的。

## <a id="setup"> </a>设置开发环境
**注意：**如果需要安装 Python 或希望使用客户端库，请参阅 [Python 安装指南](../python-how-to-install.md)。

Ubuntu Linux VM 已预安装了 Python 2.7，但它没有安装 Apache 或 Django。  按照以下步骤可连接到你的 VM 并安装 Apache 和 Django。

1. 启动新的**终端**窗口。
2. 输入以下命令来连接到 Azure VM。  如果未创建 FQDN，可使用 Azure 经典门户的虚拟机摘要中显示的公共 IP 地址进行连接。
   
       $ ssh yourusername@yourVmUrl
3. 输入以下命令来安装 Django：
   
       $ sudo apt-get install python-setuptools python-pip
       $ sudo pip install django
4. 输入以下带 mod-wsgi 的命令来安装 Apache：
   
       $ sudo apt-get install apache2 libapache2-mod-wsgi

## <a name="creating-a-new-django-application"></a>创建新的 Django 应用程序
1. 打开在上一节中使用的**终端**窗口，通过 ssh 进入你的 VM。
2. 输入以下命令来创建新的 Django 项目：
   
       $ cd /var/www
       $ sudo django-admin.py startproject helloworld
   
   **django-admin.py** 脚本生成基于 Django 的网站的基本结构：
   
   * **helloworld/manage.py** 可帮助用户启动托管和停止托管基于 Django 的网站
   * **helloworld/helloworld/settings.py** 包含你的应用程序的 Django 设置。
   * **helloworld/helloworld/urls.py** 包含每个 url 及其视图之间的映射代码。
3. 在 **/var/www/helloworld/helloworld** 目录中创建一个名为 **views.py** 的新文件。 这会包含呈现“hello world”页面的视图。 启动编辑器并输入以下代码：
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. 现在，将 **urls.py** 文件的内容替换为以下代码：
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="setting-up-apache"></a>设置 Apache
1. 创建 Apache 虚拟主机配置文件 **/etc/apache2/sites-available/helloworld.conf**。 将内容设置为以下项，并将 *yourVmName* 替换为你所使用的计算机的实际名称（例如 *pyubuntu*）。
   
       <VirtualHost *:80>
       ServerName yourVmName
       </VirtualHost>
       WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
       WSGIPythonPath /var/www/helloworld
2. 使用以下命令启用该站点：
   
       $ sudo a2ensite helloworld
3. 使用以下命令重新启动 Apache：
   
       $ sudo service apache2 reload
4. 最后，在你的浏览器中加载网页：
   
   ![显示 Azure 上的 hello world 页面的浏览器窗口](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)

## <a name="shutting-down-your-azure-virtual-machine"></a>关闭你的 Azure 虚拟机
在你完成本教程后，关闭并/或删除你新创建的 Azure 虚拟机以为其他教程释放资源并避免产生 Azure 使用费。




<!--HONumber=Nov16_HO3-->



---
title: "在 Azure Linux VM 上使用 Django 的 Python Web 应用 | Microsoft Docs"
description: "了解如何在 Azure 中使用 Linux VM 托管基于 Django 的 Web 应用。"
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
ms.date: 05/31/2017
ms.author: huvalo
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 6e2ab8c7da7496d0e2b567a4bdc9341adcf01552
ms.contentlocale: zh-cn
ms.lasthandoff: 06/22/2017


---
# <a name="django-hello-world-web-app-on-a-linux-vm"></a>Linux VM 上的 Django Hello World Web 应用
> [!div class="op_single_selector"]
> * [Windows](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Mac/Linux](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

<br>

本教程演示如何在 Azure Linux 虚拟机中托管基于 Django 的网站。 本教程假定你以前没有使用过 Azure。 完成本教程之后，将能够在云中启用和运行基于 Django 的应用程序。

了解如何：

* 设置 Azure 虚拟机以托管 Django。 虽然本教程介绍如何在 Linux 中执行此操作，但也可在 Azure 托管的 Windows Server VM 中执行相同操作。 
* 在 Linux 中新建 Django 应用程序。

本教程演示如何生成基础 Hello World Web 应用程序。 该应用程序托管在 Azure 虚拟机中。

以下屏幕快照显示已完成的应用程序：

![浏览器窗口显示 Azure 中的 Hello World 页面](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

[!INCLUDE [create-account-and-vms-note](../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>创建并设置 Azure 虚拟机以托管 Django

1. 若要使用 Ubuntu Server 14.04 LTS 分发创建 Azure 虚拟机，请参阅[在 Azure 门户中创建 Linux 虚拟机](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 还可选择密码验证而不是使用 SSH 公钥。
2. 若要编辑网络安全组以允许端口 80 的传入 HTTP 流量，请参阅[在 Azure 门户中创建网络安全组](../../virtual-network/virtual-networks-create-nsg-arm-pportal.md)。
3. （可选）默认情况下，新的虚拟机没有完全限定的域名 (FQDN)。  若要创建具有 FQDN 的 VM，请参阅[在 Azure 门户为 Windows VM 创建 FQDN](../windows/portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 此步骤非本教程必需步骤。

## <a id="setup"> </a>设置开发环境
> [!NOTE]
> 如果需要安装 Python 或者想使用客户端库，请参阅 [Python 安装指南](../../python-how-to-install.md)。

Ubuntu Linux VM 已预装 Python 2.7，但不附带 Apache 或 Django。 完成以下步骤连接到 VM 并安装 Apache 和 Django：

1. 打开新终端窗口。
2. 输入以下命令连接到 Azure VM。 如未创建 FQDN，可使用 Azure 门户的虚拟机摘要中显示的公共 IP 地址进行连接。
   
       $ ssh yourusername@yourVmUrl
3. 输入以下命令来安装 Django：
   
       $ sudo apt-get install python-setuptools python-pip
       $ sudo pip install django
4. 输入以下命令来安装带 mod-wsgi 的 Apache：
   
       $ sudo apt-get install apache2 libapache2-mod-wsgi

## <a name="create-a-new-django-app"></a>创建新的 Django 应用
1. 若要使用 SSH 访问 VM，请打开上一节中使用的终端窗口。
2. 输入以下命令来创建新的 Django 项目：
   
       $ cd /var/www
       $ sudo django-admin.py startproject helloworld
   
   `django-admin.py` 脚本生成基于 Django 的网站的基本结构：
   
   * `helloworld/manage.py` 帮助你开始托管和停止托管基于 Django 的网站。
   * `helloworld/helloworld/settings.py` 包含应用程序的 Django 设置。
   * `helloworld/helloworld/urls.py` 包含每个 URL 及其视图之间的映射代码。
3. 在 /var/www/helloworld/helloworld 目录中创建一个名为 views.py 的新文件。 这个文件包含呈现“hello world”页面的视图。 在代码编辑器中输入以下命令：
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. 使用以下命令替换 urls.py 文件的内容：
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-apache"></a>设置 Apache
1. 在 /etc/apache2/sites-available/helloworld.conf 文件夹中创建 Apache 虚拟主机配置文件。 将内容设置为以下值。 将“yourVmName” 替换为所使用的计算机的实际名称（例如“pyubuntu”）。
   
       <VirtualHost *:80>
       ServerName yourVmName
       </VirtualHost>
       WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
       WSGIPythonPath /var/www/helloworld
2. 使用以下命令激活该站点：
   
       $ sudo a2ensite helloworld
3. 使用以下命令重新启动 Apache：
   
       $ sudo service apache2 reload
4. 在浏览器中加载网页：
   
   ![浏览器窗口显示 Azure 中的 Hello World 页面](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

## <a name="shut-down-your-azure-virtual-machine"></a>关闭 Azure 虚拟机
完成本教程后，建议你关闭或删除为此教程创建的 Azure VM。 这将为其他教程释放资源，同时可以避免产生 Azure 使用费用。



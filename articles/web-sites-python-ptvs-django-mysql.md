<properties linkid="web-sites-python-ptvs-django-mysql" title="具有 Python Tools 2.1 for Visual Studio 的 Azure 上的 Django 和 MySQL" pageTitle="具有 Python Tools 2.1 for Visual Studio 的 Azure 上的 Django 和 MySQL" description="了解如何使用 Python Tools for Visual Studio 来创建 Django 应用程序，该应用程序在 MySQL 数据库实例中存储数据并且可以部署到网站。" metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# 具有 Python Tools 2.1 for Visual Studio 的 Azure 上的 Django 和 MySQL 

在本教程中，我们将使用 PTVS 样本模板之一创建一个简单轮询应用程序。还以[视频](https://www.youtube.com/watch?v=oKCApIrS0Lo)形式提供了本教程。

我们将了解如何使用在 Azure 上托管的 MySQL 服务、如何配置此应用程序使用 MySQL 和如何将应用程序发布到 Azure 网站。

请参阅 [Python 开发人员中心][]以获取更多文章，这些文章涵盖了与使用 PTVS、Bottle、Flask 和 Django web 框架开发 Azure 网站，使用 MongoDB、 Azure 表存储、 MySQL 和 SQL 数据库服务来开发 Azure 网站。虽然本文将着重介绍在 Azure 网站，但是在开发 [Azure 云服务][]，步骤相似。

+ [先决条件](#prerequisites)
+ [创建项目](#create-the-project)
+ [创建 MySQL 数据库](#create-a-mysql-database)
+ [配置项目](#configure-the-project)
+ [发布到 Azure 网站](#publish-to-an-azure-website)
+ [后续步骤](#next-steps)

## <a name="prerequisites"></a>先决条件

 - Visual Studio 2012 或 2013
 - [Python Tools 2.1 for Visual Studio][]
 - [Python Tools 2.1 for Visual Studio 样本 VSIX][]
 - [Azure SDK Tools for VS 2013][] 或 [Azure SDK Tools for VS 2012][]
 - [Python 2.7（32 位）][]

> [AZURE.NOTE]
> 要完成本教程，您需要一个 Azure 帐户。您可以 <a href="/zh-cn/pricing/member-offers/msdn-benefits-details/">激活 MSDN 订户权益</a> 或 <a href="/zh-cn/pricing/1rmb-trial/">注册免费试用版</a>。
> 
> 如果您想要在注册帐户之前开始使用 Azure 网站，请转到 <a href="https://trywebsites.chinacloudsites.cn/?language=python">https://trywebsites.chinacloudsites.cn</a>其中您可以在 Azure 网站中立即免费创建短期 ASP.NET 入门网站。无需信用卡，没有承诺。

## <a name="create-the-project"></a>创建项目

在此部分中，我们将使用样本模板创建 Visual Studio 项目。我们将创建虚拟环境并安装所需软件包。我们将创建一个使用 sqlite 的本地数据库。然后我们将在本地运行应用程序。

1.  在 Visual Studio 中，选择**文件**、**新建项目**。

1.  在 **Python**、**Samples** 下提供来自 PTVS 样本 VSIX 的项目模板。选择**轮询 Django Web 项目**，然后单击"确定"以创建项目。

  	![New Project Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  系统将提示您安装外部软件包。选择**安装到虚拟环境**。

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  选择 **Python 2.7** 作为基础解释程序。

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  右键单击项目节点并选择 **Python**、**Django 同步数据库**。

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  这将打开 Django 管理控制台。按照提示操作以创建一个用户。

    这将在项目文件夹中创建一个 sqlite 数据库。

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  按 <kbd>F5</kbd> 以确认该应用程序有效。

1.  从顶部导航栏单击**登录**。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  输入您同步数据库时创建的用户的凭证。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  单击**创建示例轮询**。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  单击轮询并进行投票。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## <a name="create-a-mysql-database"></a>创建 MySQL 数据库

对于数据库，我们将在 Azure 上创建 ClearDB MySQL 托管数据库。

作为替代方法，可以在 Azure 上创建自己的虚拟机，然后亲自安装和管理 MySQL。

可以通过执行以下步骤以免费计划创建数据库。

1.  登录到 [Azure 管理门户][]。

1.  在导航窗格的底部，单击"新建"。

  	![New Button](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  单击**存储**，然后单击 **ClearDB MySQL 数据库**。

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  在名称中，键入要用于数据库服务的名称。

1.  选择要在其中查找数据库服务的区域/地缘组。如果将从您的 Azure 应用程序使用数据库，请选择要在其中部署您的应用程序的同一区域。

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  单击**采购**。

## <a name="configure-the-project"></a>配置项目

在此部分中，我们将配置应用程序以使用我们刚刚创建的 MySQL 数据库。我们将了解如何从 Azure 门户中获取连接设置。我们还将安装使用具有 Django 的 MySQL 数据库所需的其他 Python 软件包。然后我们将在本地运行应用程序。

1.  在 [Azure 管理门户][]中，请单击**外接程序**，然后单击先前创建的 ClearDB MySQL 数据库服务。

1.  单击**连接信息**。可以使用复制按钮将 **CONNECTIONSTRING** 的值置于剪贴板上。

  	![Connection Info Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  在 Visual Studio 中，从  *ProjectName* 文件夹打开 **settings.py**。暂时将连接字符串粘贴在编辑器中。连接字符串是按以下格式：

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    更改默认的数据库**引擎**以使用 MySQL，然后从 **CONNECTIONSTRING** 设置**名称**、**用户**、**密码**和**主机**的值。

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }


1.  在 Solution Explorer 中的 **Python 环境**下，右键单击虚拟环境并选择**安装 Python 软件包**。

1. 使用 **easy_install** 安装软件包  `mysql-python`。

  	![Install Package Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  右键单击项目节点并选择 **Python**、**Django 同步数据库**。  

    这将创建我们在上一节中创建的 MySQL 数据库的表。按照提示操作以创建用户，其并不一定要匹配第一节中创建的 sqlite 数据库中的用户。

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  使用 <kbd>F5</kbd> 来运行应用程序。使用**创建样本轮询**来创建的轮询以及通过投票提交的数据将在 MySQL 数据库中序列化。

## <a name="publish-to-an-azure-website"></a>发布到 Azure 网站

PTVS 提供了将 web 应用程序部署到 Azure 网站的方便方法。

1.  在 **Solution Explorer** 中，右键单击项目节点并选择**发布**。

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  单击 **Microsoft Azure 网站**。

1.  单击**新建**以创建一个新网站。

1.  选择**站点名称**和**区域**，并单击**创建**。

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  接受其他所有默认值，然后单击**发布**。

1.  Web 浏览器会自动打开到已发布的站点。您应该看到应用程序使用在 Azure 上托管的 **MySQL** 数据库按预期方式工作。

    祝贺您！

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

## <a name="next-steps"></a>后续步骤

请按照下面的链接以了解有关 Python Tools for Visual Studio、Django 和 MySQL 的更多信息。

- [Python Tools for Visual Studio 文档][]
  - [Web 项目][]
  - [云服务项目][]
  - [在 Microsoft Azure 上的远程调试][]
- [Django 文档][]
- [MySQL][]


<!--Link references-->
[Python 开发人员中心]: /zh-cn/develop/python/
[Azure 云服务]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Azure 管理门户]: https://manage.windowsazure.cn
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 for Visual Studio 样本 VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7（32 位）]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python Tools for Visual Studio 文档]: http://pytools.codeplex.com/documentation
[在 Microsoft Azure 上的远程调试]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Web 项目]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[云服务项目]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Django 文档]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/

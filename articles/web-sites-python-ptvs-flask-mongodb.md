<properties linkid="web-sites-python-ptvs-flask-mongodb" title="Flask and MongoDB on Azure with Python Tools 2.1 for Visual Studio" pageTitle="具有 Python Tools 2.1 for Visual Studio 的 Azure 上的 Flask 和 MongoDB" description="了解如何使用 Python Tools for Visual Studio 来创建 Flask 应用程序，该应用程序在 MongoDB 数据库实例中存储数据并且可以部署到网站。" metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# 具有 Python Tools 2.1 for Visual Studio 的 Azure 上的 Flask 和 MongoDB

在本教程中，我们将使用 PTVS 样本模板之一创建一个简单轮询应用程序。还以[视频](https://www.youtube.com/watch?v=eql-crFgrAE)形式提供了本教程。

轮询应用程序定义其存储库的抽象，因此您可以轻松地切换不同类型存储库（In-Memory、Azure 表存储 MongoDB）之间。

我们将了解如何使用在 Azure 上托管的 MongoDB 服务、如何配置此应用程序使用 MongoDB 和如何将应用程序发布到 Azure 网站。

请参阅 [Python 开发人员中心][]以获取更多文章，这些文章涵盖了与使用 PTVS、Bottle、Flask 和 Django web 框架开发 Azure 网站，使用 MongoDB、 Azure 表存储、 MySQL 和 SQL 数据库服务来开发 Azure 网站。虽然本文将着重介绍在 Azure 网站，但是在开发 [Azure 云服务][]，步骤相似。

+ [先决条件](#prerequisites)
+ [创建项目](#create-the-project)
+ [创建 MongoDB 数据库](#create-a-mongodb-database)
+ [配置项目](#configure-the-project)
+ [了解 MongoDB 数据库](#explore-the-mongodb-database)
+ [发布到 Azure 网站](#publish-to-an-azure-website)
+ [配置 Azure 网站](#configure-the-azure-website)
+ [后续步骤](#next-steps)

## <a name="prerequisites"></a>先决条件

 - Visual Studio 2012 或 2013
 - [Python Tools 2.1 for Visual Studio][]
 - [Python Tools 2.1 for Visual Studio 样本 VSIX][]
 - [Azure SDK Tools for VS 2013][] 或 [Azure SDK Tools for VS 2012][]
 - [Python 2.7 32 位][]或[Python 3.4 32 位][]
 - [RoboMongo][] （可选）

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## <a name="create-the-project"></a>创建项目

在此部分中，我们将使用样本模板创建 Visual Studio 项目。我们将创建虚拟环境并安装所需软件包。然后，我们将使用默认内存中存储库在本地运行应用程序。

1.  在 Visual Studio 中，选择**文件**、**新建项目**。 

1.  在 **Python**、**Samples** 下提供来自 PTVS 样本 VSIX 的项目模板。选择**轮询 Flask Web 项目**，然后单击"确定"以创建项目。

  	![New Project Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskNewProject.png)

1.  系统将提示您安装外部软件包。选择**安装到虚拟环境**。

  	![External Packages Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskExternalPackages.png)

1.  选择 **Python 2.7** 或 **Python 3.4** 作为基础解释程序。

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAddVirtualEnv.png)

1.  按 <kbd>F5</kbd> 以确认该应用程序有效。默认情况下，该应用程序使用内存中存储库，这并不需要任何配置。停止 web 服务器时，所有数据都会丢失。

1.  单击**创建样本轮询**，然后单击轮询并进行投票。

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskInMemoryBrowser.png)

## <a name="create-a-mongodb-database"></a>创建 MongoDB 数据库

对于数据库，我们将在 Azure 上创建 MongoLab 托管数据库。

作为替代方法，可以在 Azure 上创建自己的虚拟机，然后亲自安装和管理 MongoDB。

通过执行以下步骤，可以使用 MongoLab 创建免费试用版。

1.  登录到 [Azure 管理门户][]。

1.  在导航窗格的底部，单击"新建"。

  	![New Button](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAzurePlusNew.png)

1.  单击**存储**，然后单击 **MongoLab**。

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon1.png)

1.  在名称中，键入要用于数据库服务的名称。

1.  选择要在其中查找数据库服务的区域/地缘组。如果将从您的 Azure 应用程序使用数据库，请选择要在其中部署您的应用程序的同一区域。

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon2.png)

1.  单击**采购**。

## <a name="configure-the-project"></a>配置项目

在此部分中，我们将配置应用程序以使用我们刚刚创建的 MongoDB 数据库。我们将了解如何从 Azure 门户中获取连接设置。然后我们将在本地运行应用程序。

1.  在 [Azure 管理门户][]中，请单击**外接程序**，然后单击先前创建的 MongoLab 服务。

1.  单击**连接信息**。可以使用复制按钮将 **MONGOLAB\_URI** 的值置于剪贴板上。

  	![Connection Info Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  在 Visual Studio 中，右键单击 Solution Explorer 中的项目节点，然后选择**属性**。单击**调试**选项卡。

  	![Project Debug Settings](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBProjectDebugSettings.png)

1.  在**调试服务器命令**，**环境**中设置应用程序所需的环境变量的值。

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    您**启动调试**时，这会设置环境变量。  如果您**启动但不调试**希望设置变量，还在**运行服务器命令**下设置相同值。

    或者，可以使用 Windows 控制面板来定义环境变量。如果您想要避免将凭证存储在源代码中/项目文件中，这是更好的选择。请注意，您将需要重新启动 Visual Studio 以使新环境值可用于应用程序。

1.  实现 MongoDB 存储库的代码都在 **models/mongodb.py** 中。

1.  使用 <kbd>F5</kbd> 来运行应用程序。使用**创建样本轮询**来创建的轮询以及通过投票提交的数据将在 MongoDB 中序列化。

1.  浏览到**有关**页以验证该应用程序是否正在使用 **MongoDB** 存储库。

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBAbout.png)

## <a name="explore-the-mongodb-database"></a>了解 MongoDB 数据库

您可以使用应用程序（如 [RoboMongo][]）来查询和编辑 MongoDB 数据库。本部分中，我们将使用 RoboMongo 来查看轮询应用程序数据库的内容。

1.  创建新的连接。您将需要在上一节中检索的 **MONGOLAB\_URI** 。

    请注意 URI 的格式： `mongodb://<name>:<password>@<address>:<port>/<name>`

    该名称匹配了使用 Azure 创建服务时您输入的名称。它用于数据库名称和用户名。

1.  在连接页面中，将**名称**设置为您要用于此连接的任何名称。还将**地址**和**端口**字段设置为来自 **MONGOLAB\_URI** 的 *address*和 *port*。

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  在身份验证页中，将**数据库**和**用户名**为来自 **MONGOLAB\_URI** 的  *name*。还将**密码**设置为来自 **MONGOLAB\_URI** 的 *password*。

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  保存并连接到数据库。现在可以查询轮询集合。

  	![RoboMongo Query Results](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoQuery.png)

## <a name="publish-to-an-azure-website"></a>发布到 Azure 网站

PTVS 提供了将 web 应用程序部署到 Azure 网站的方便方法。

1.  在 **Solution Explorer** 中，右键单击项目节点并选择**发布**。

  	![Publish Web Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonPublishWebSiteDialog.png)

1.  单击 **Microsoft Azure 网站**。

1.  单击**新建**以创建一个新网站。

1.  选择**站点名称**和**区域**，并单击**创建**。

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonCreateWebSite.png)

1.  接受其他所有默认值，然后单击**发布**。

1.  Web 浏览器会自动打开到已发布的站点。如果浏览到"有关"页，您将看到它使用**内存中**存储库中，不是 **MongoDB** 存储库。

    这是因为未在 Azure 网站中设置环境变量，因此它将使用 **settings.py** 中指定的默认值。

## <a name="configure-the-azure-website"></a>配置 Azure 网站

在此部分中，我们将配置该站点的环境变量。

1.  在 [Azure 管理门户][]中，单击在上一节中创建的网站。

1.  在顶部菜单中，单击**配置**。

  	![Top Menu](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteTopMenu.png)

1.  向下滚动到**应用设置**部分和并设置 **REPOSITORY\_NAME**、**MONGODB\_HOST** 和 **MONGODB\_DATABASE** 的值（如上一节中所述）。

  	![App Settings](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png)

1.  在底部菜单中，单击**保存**，然后单击**重新启动**，最后单击**浏览**。

  	![Bottom Menu](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureBottomMenu.png)

1.  您应该看到应用程序使用 **MongoDB** 存储库按预期方式工作。

    祝贺您！

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskAzureBrowser.png)

## <a name="next-steps"></a>后续步骤

请按照下面的链接以了解有关 Python Tools for Visual Studio、Flask 和 MongoDB 的更多信息。

- [Python Tools for Visual Studio 文档][]
  - [Web 项目][]
  - [云服务项目][]
  - [在 Microsoft Azure 上的远程调试][]
- [Flask 文档][]
- [MongoDB][]
- [PyMongo 文档][]
- [PyMongo][]


<!--Link references-->
[Python 开发人员中心]: /zh-cn/develop/python/
[Azure 云服务]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Azure 管理门户]: https://manage.windowsazure.cn
[RoboMongo]: http://robomongo.org/
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 for Visual Studio 样本 VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 位]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 32 位]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools for Visual Studio 文档]: http://pytools.codeplex.com/documentation
[Flask 文档]: http://flask.pocoo.org/
[MongoDB]: http://www.mongodb.org/
[PyMongo 文档]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[在 Microsoft Azure 上的远程调试]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Web 项目]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[云服务项目]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project

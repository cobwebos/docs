<properties linkid="web-sites-python-ptvs-bottle-table-storage" title="具有 Python Tools 2.1 for Visual Studio 的 Azure 上的 Bottle 和 Azure 表存储" pageTitle="具有 Python Tools 2.1 for Visual Studio 的 Azure 上的 Bottle 和 Azure 表存储" description="了解如何使用 Python Tools for Visual Studio 来创建 Bottle 应用程序，该应用程序在 Azure 表存储中存储数据并且可以部署到网站。" metaKeywords="" services="web-sites" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# 具有 Python Tools 2.1 for Visual Studio 的 Azure 上的 Bottle 和 Azure 表存储 

在本教程中，我们将使用 PTVS 样本模板之一创建一个简单轮询应用程序。还以[视频](https://www.youtube.com/watch?v=GJXDGaEPy94)形式提供了本教程。

轮询应用程序定义其存储库的抽象，因此您可以轻松地切换不同类型存储库（In-Memory、Azure 表存储 MongoDB）之间。

我们将学习如何创建 Azure 存储帐户、如何配置应用程序以使用 Azure 表存储以及如何将应用程序发布到 Azure 网站。

请参阅 [Python 开发人员中心][]以获取更多文章，这些文章涵盖了与使用 PTVS、Bottle、Flask 和 Django web 框架开发 Azure 网站，使用 MongoDB、 Azure 表存储、 MySQL 和 SQL 数据库服务来开发 Azure 网站。虽然本文将着重介绍在 Azure 网站，但是在开发 [Azure 云服务][]，步骤相似。

+ [先决条件](#prerequisites)
+ [创建项目](#create-the-project)
+ [创建 Azure 存储帐户](#create-an-azure-storage-account)
+ [配置项目](#configure-the-project)
+ [了解 Azure 表存储](#explore-the-azure-table-storage)
+ [发布到 Azure 网站](#publish-to-an-azure-website)
+ [配置 Azure 网站](#configure-the-azure-website)
+ [后续步骤](#next-steps)

## <a name="prerequisites"></a>先决条件

 - Visual Studio 2012 或 2013
 - [Python Tools 2.1 for Visual Studio][]
 - [Python Tools 2.1 for Visual Studio 样本 VSIX][]
 - [Azure SDK Tools for VS 2013][] 或 [Azure SDK Tools for VS 2012][]
 - [Python 2.7 32 位][]或[Python 3.4 32 位][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## <a name="create-the-project"></a>创建项目

在此部分中，我们将使用样本模板创建 Visual Studio 项目。我们将创建虚拟环境并安装所需软件包。然后，我们将使用默认内存中存储库在本地运行应用程序。

1.  在 Visual Studio 中，选择**文件**、**新建项目**。

1.  在 **Python**、**Samples** 下提供来自 PTVS 样本 VSIX 的项目模板。选择**轮询 Bottle Web 项目**，然后单击"确定"以创建项目。

  	![New Project Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleNewProject.png)

1.  系统将提示您安装外部软件包。选择**安装到虚拟环境**。

  	![External Packages Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleExternalPackages.png)

1.  选择 **Python 2.7** 或 **Python 3.4** 作为基础解释程序。

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAddVirtualEnv.png)

1.  按 <kbd>F5</kbd> 以确认该应用程序有效。默认情况下，该应用程序使用内存中存储库，这并不需要任何配置。停止 web 服务器时，所有数据都会丢失。

1.  单击**创建样本轮询**，然后单击轮询并进行投票。

  	![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleInMemoryBrowser.png)

## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

要使用存储操作，你需要一个 Azure 存储帐户。可通过以下步骤创建存储帐户。

1.  登录到 [Azure 管理门户][]。

1.  在导航窗格的底部，单击"新建"。

  	![New Button](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzurePlusNew.png)

1.  依次单击"数据服务"、"存储"和"快速创建"。

  	![Quick Create](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzureStorageCreate.png)

1.  在 URL 中，键入要在存储帐户的 URI 中使用的子域名称。输入名称可包含 3-24 个小写字母和数字。此值将成为用于对订阅的 Blob、队列或表资源进行寻址的 URI 中的主机名。

1.  选择要在其中查找存储的区域/地缘组。如果将从您的 Azure 应用程序使用存储，请选择要在其中部署您的应用程序的同一区域。

1.  还可以选择启用地域复制。凭借异地复制，Azure 存储现在将您的数据持久保存在两个位置。在这两个位置，Azure 存储始终维护您数据的多个运行正常副本。

1.  单击"创建存储帐户"。

## <a name="configure-the-project"></a>配置项目

在此部分中，我们将配置应用程序以使用我们刚刚创建的存储帐户。我们将了解如何从 Azure 门户中获取连接设置。然后我们将在本地运行应用程序。

1.  在 [Azure 管理门户][]中，单击在上一节中创建的存储帐户。

1.  单击**管理访问密钥**。

  	![Manage Access Keys Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzureTableStorageManageKeys.png)

1.  在 Visual Studio 中，右键单击 Solution Explorer 中的项目节点，然后选择**属性**。单击**调试**选项卡。

  	![Project Debug Settings](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageProjectDebugSettings.png)

1.  在**调试服务器命令**，**环境**中设置应用程序所需的环境变量的值。

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    您**启动调试**时，这会设置环境变量。  如果您**启动但不调试**希望设置变量，还在**运行服务器命令**下设置相同值。

    或者，可以使用 Windows 控制面板来定义环境变量。如果您想要避免将凭证存储在源代码中/项目文件中，这是更好的选择。请注意，您将需要重新启动 Visual Studio 以使新环境值可用于应用程序。

1.  实现 Azure 表存储库中的代码在 **models/azuretablestorage.py** 中。请参阅[文档]以了解如何从 Python 使用表服务的更多信息。

1.  使用 <kbd>F5</kbd> 来运行应用程序。使用**创建样本轮询**来创建的轮询以及通过投票提交的数据将在 Azure 表存储中序列化。

1.  浏览到**有关**页以验证该应用程序是否正在使用 **Azure 表存储**库。

  	![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageAbout.png)

## <a name="explore-the-azure-table-storage"></a>了解 Azure 表存储

很容易地使用 Visual Studio 中的 Server Explorer 查看和编辑存储表。  本部分中，我们将使用 Server Explorer 查看轮询应用程序表的内容。

> [AZURE.NOTE] 这要求安装 Microsoft Azure 工具，这些可作为 [Azure SDK for.NET][] 的一部分而提供。

1.  打开 **Server Explorer**。展开 **Azure**、**存储**，您的存储帐户，然后展开**表**。

  	![Server Explorer](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorer.png)

1.  双击**轮询**或**选择**表以在文档窗口中查看内容，以及添加/删除/编辑实体。

  	![Table Query Results](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorerTable.png)

## <a name="publish-to-an-azure-website"></a>发布到 Azure 网站

PTVS 提供了将 web 应用程序部署到 Azure 网站的方便方法。

1.  在 **Solution Explorer** 中，右键单击项目节点并选择**发布**。

  	![Publish Web Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  单击 **Microsoft Azure 网站**。

1.  单击**新建**以创建一个新网站。

1.  选择**站点名称**和**区域**，并单击**创建**。

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonCreateWebSite.png)

1.  接受其他所有默认值，然后单击**发布**。

1.  Web 浏览器会自动打开到已发布的站点。如果浏览到"有关"页，您将看到它使用**内存中**存储库中，不**Azure 表存储**存储库。

    这是因为未在 Azure 网站中设置环境变量，因此它将使用 **settings.py** 中指定的默认值。

## <a name="configure-the-azure-website"></a>配置 Azure 网站

在此部分中，我们将配置该站点的环境变量。

1.  在 [Azure 管理门户][]中，单击在上一节中创建的网站。

1.  在顶部菜单中，单击**配置**。

  	![Top Menu](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteTopMenu.png)

1.  向下滚动到**应用设置**部分和并设置 **REPOSITORY\_NAME**、**STORAGE\_NAME** 和 **STORAGE\_KEY** 的值（如上一节中所述）。

  	![App Settings](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1. 在底部菜单中，单击**保存**，然后单击**重新启动**，最后单击**浏览**。

  	![Bottom Menu](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureBottomMenu.png)

1.  您应该看到应用程序按预期方式使用 **Azure 表存储**存储库工作。

    祝贺您！

  	![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureBrowser.png)

## <a name="next-steps"></a>后续步骤

请按照下面的链接以了解有关 Python Tools for Visual Studio、 Bottle 和 Azure 表存储的更多信息。

- [Python Tools for Visual Studio 文档][]
  - [Web 项目][]
  - [云服务项目][]
  - [在 Microsoft Azure 上的远程调试][]
- [Bottle 文档][]
- [Azure 存储][]
- [Azure SDK for Python][]
- [如何从 Python 使用表存储服务][]


<!--Link references-->
[Python 开发人员中心]: /zh-cn/develop/python/
[Azure 云服务]: ../cloud-services-python-ptvs/
[文档]: ../storage-python-how-to-use-table-storage/
[如何从 Python 使用表存储服务]: ../storage-python-how-to-use-table-storage/

<!--External Link references-->
[Azure 管理门户]: https://manage.windowsazure.cn
[Azure SDK for.NET]: /zh-cn/downloads/
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 for Visual Studio 样本 VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 位]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 32 位]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools for Visual Studio 文档]: http://pytools.codeplex.com/documentation
[Bottle 文档]: http://bottlepy.org/docs/dev/index.html
[在 Microsoft Azure 上的远程调试]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Web 项目]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[云服务项目]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Azure 存储]: /zh-cn/documentation/services/storage/
[Azure SDK for Python]: https://github.com/Azure/azure-sdk-for-python

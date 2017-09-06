---
title: "具有 Python Tools 2.2 for Visual Studio 的 Azure 上的 Flask 和 Azure 表存储"
description: "了解如何使用 Python Tools for Visual Studio 来创建在 Azure 表存储中存储数据的 Flask Web 应用，以及将应用部署到 Azure 应用服务 Web 应用中。"
services: app-service\web
tags: python
documentationcenter: python
author: huguesv
manager: erikre
editor: 
ms.assetid: d8e70a29-aca1-4010-95f5-cfe769e3be06
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 07/07/2016
ms.author: huvalo
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 2e1bc8eebd0b67b965cc70ac4b5dfe03c4720ddf
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="flask-and-azure-table-storage-on-azure-with-python-tools-22-for-visual-studio"></a>具有 Python Tools 2.2 for Visual Studio 的 Azure 上的 Flask 和 Azure 表存储
在本教程中，我们将使用 [Python Tools for Visual Studio] 通过一个 PTVS 样本模板创建简单的轮询 Web 应用。 本教程也作为[视频](https://www.youtube.com/watch?v=qUtZWtPwbTk)提供。

轮询 Web 应用定义其存储库的抽象，因此可以轻松地在不同类型存储库（内存中、Azure 表存储、MongoDB）之间进行切换。

我们将了解如何创建 Azure 存储帐户、如何将 Web 应用配置为使用 Azure 表存储，以及如何将 Web 应用发布到 [Azure 应用服务 Web 应用](http://go.microsoft.com/fwlink/?LinkId=529714)中。

请参阅 [Python 开发人员中心]以获取更多文章，这些文章介绍了如何通过 PTVS（使用 Bottle、Flask 和 Django Web 框架）、MongoDB、Azure 表存储、MySQL 和 SQL 数据库服务开发 Azure 应用服务 Web 应用。 虽然本文将着重介绍应用服务，但步骤与 [Azure 云服务]的开发步骤类似。

## <a name="prerequisites"></a>先决条件
* Visual Studio 2015
* [Python Tools 2.2 for Visual Studio]
* [Python Tools 2.2 for Visual Studio 示例 VSIX]
* [Azure SDK Tools for VS 2015]
* [Python 2.7（32 位）]或 [Python 3.4（32 位）]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> 如果要在注册 Azure 帐户之前开始使用 Azure 应用服务，请转到[试用应用服务](https://azure.microsoft.com/try/app-service/)，可以在应用服务中立即创建一个生存期较短的入门 Web 应用。 不需要使用信用卡，也不需要做出承诺。
> 
> 

## <a name="create-the-project"></a>创建项目
在此部分中，我们将使用样本模板创建 Visual Studio 项目。 我们将创建虚拟环境并安装所需软件包。 然后，我们将使用默认内存中存储库在本地运行应用程序。

1. 在 Visual Studio 中，选择“文件”“新建项目”。
2. 可以从“Python”“示例”下面获取[Python Tools 2.2 for Visual Studio 示例 VSIX]中的项目模板。 选择“轮询 Flask Web 项目”，并单击“确定”创建项目。
   
     ![“新建项目”对话框](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)
3. 系统会提示安装外部软件包。 选择 **安装到虚拟环境**。
   
     ![外部包对话框](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)
4. 选择“Python 2.7”或“Python 3.4”作为基础解释器。
   
     ![添加虚拟环境对话框](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)
5. 按 `F5`确认应用程序能否正常运行。 默认情况下，该应用程序使用内存中存储库，这并不需要任何配置。 停止 web 服务器时，所有数据都会丢失。
6. 单击“创建样本轮询”，并单击一个轮询进行投票。
   
     ![Web 浏览器](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户
要使用存储操作，需要一个 Azure 存储帐户。 可通过以下步骤创建存储帐户。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 单击门户左上角的“新建”图标，并依次单击“数据 + 存储” > “存储帐户”。 单击“创建”，为存储帐户指定一个唯一名称，并为它创建新的[资源组](../azure-resource-manager/resource-group-overview.md)。
   
      ![快速创建](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)
   
    创建存储帐户后，“通知”按钮将呈绿色闪烁表示**成功**，并且存储帐户的边栏选项卡处于打开状态，以显示它属于所创建的新资源组。
3. 在存储帐户的边栏选项卡中单击“访问密钥”部分。 记下帐户名和 key1。
   
      ![密钥](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageKeys.png)
   
    我们将需要使用此信息在下一部分中配置项目。

## <a name="configure-the-project"></a>配置项目
在此部分中，我们将配置应用程序以使用我们刚刚创建的存储帐户。 我们将了解如何从 Azure 门户中获取连接设置。 然后我们会在本地运行应用程序。

1. 在 Visual Studio 中，右键单击解决方案资源管理器中的项目节点，并选择“属性”。 单击“调试”选项卡。
   
     ![项目调试设置](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)
2. 在“调试服务器命令”、“环境”中设置应用程序所需的环境变量的值。
   
       REPOSITORY_NAME=azuretablestorage
       STORAGE_NAME=<storage account name>
       STORAGE_KEY=<primary access key>
   
   这会**开始调试**时设置环境变量。 如果希望在**启动但不调试**时设置变量，还需要在“运行服务器命令”下设置相同值。
   
   或者，可以使用 Windows 控制面板来定义环境变量。 如果想要避免将凭证存储在源代码中/项目文件中，这是更好的选择。 请注意，需要重新启动 Visual Studio 以使新环境值可用于应用程序。
3. 实施 Azure 表存储库的代码位于 **models/azuretablestorage.py** 中。 请参阅[文档]，了解有关如何从 Python 使用表服务的详细信息。
4. 使用 `F5`运行应用程序。 使用“创建样本轮询”创建的轮询以及通过投票提交的数据会在 Azure 表存储中进行序列化。
   
   > [!NOTE]
   > 在 Visual Studio 中，Python 2.7 虚拟环境可能会导致异常中断。  按 `F5` 继续加载该 Web 项目。
   > 
   > 
5. 浏览到“关于”页面，验证应用程序是否在使用 **Azure 表存储**存储库。
   
     ![Web 浏览器](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

## <a name="explore-the-azure-table-storage"></a>了解 Azure 表存储
使用 Visual Studio 中的云资源管理器可以轻松查看和编辑存储表。 本部分中，我们将使用 Server Explorer 查看轮询应用程序表的内容。

> [!NOTE]
> 这要求安装 Microsoft Azure 工具，这些可作为 [用于 .NET 的 Azure SDK] 的一部分提供。
> 
> 

1. 打开“云资源管理器”。 展开“存储帐户”、存储帐户、“表”。
   
     ![云资源管理器](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorer.png)
2. 双击“轮询”或“选择”表，在文档窗口中查看表的内容，以及添加/删除/编辑实体。
   
     ![表查询结果](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorerTable.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>将 Web 应用发布到 Azure 应用服务
借助 Azure.NET SDK，可以轻松地将 Web 应用部署到 Azure 应用服务中。

1. 在“解决方案资源管理器”中，右键单击项目节点，并选择“发布”。
   
     ![发布 Web 对话框](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonPublishWebSiteDialog.png)
2. 单击“Microsoft Azure Web 应用”。
3. 单击“新建”  ，新建一个 Web 应用。
4. 填写以下字段，并单击“创建”。
   
   * **Web 应用名称**
   * **应用服务计划**
   * **资源组**
   * **区域**
   * 保持“数据库服务器”的“无数据库”设置不变
5. 接受其他所有默认值，并单击 **发布**。
6. 此时，Web 浏览器会自动打开已发布的 Web 应用。 如果浏览到“关于”页面，将看到它使用的是**内存中**存储库，而不是 **Azure 表存储**存储库。
   
   这是因为未在 Azure 应用服务的 Web 应用实例上设置环境变量，因此它使用的是 **settings.py** 中指定的默认值。

## <a name="configure-the-web-apps-instance"></a>配置 Web 应用实例
在此部分中，我们将配置 Web 应用实例的环境变量。

1. 在 [Azure 门户](https://portal.azure.com)中，依次单击“浏览” > “应用服务”> Web 应用名称，打开 Web 应用的边栏选项卡。
2. 在 Web 应用的边栏选项卡中，依次单击“所有设置”和“应用程序设置”。
3. 向下滚动到“应用设置”部分并设置 **REPOSITORY\_NAME**、**STORAGE\_NAME** 和 **STORAGE\_KEY** 的值（如上面的**配置项目**部分中所述）。
   
     ![应用设置](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)
4. 单击“保存” 。 收到更改已应用的通知后，单击 Web 应用主边栏选项卡中的“浏览”。
5. 应该会看到 Web 应用使用 **Azure 表存储**存储库按预期方式运行。
   
   祝贺你！
   
     ![Web 浏览器](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

## <a name="next-steps"></a>后续步骤
请按照下面的链接以了解有关 Python Tools for Visual Studio、 Flask 和 Azure 表存储的更多信息。

* [Python Tools for Visual Studio 文档]
  * [Web 项目]
  * [云服务项目]
  * [在 Microsoft Azure 上进行远程调试]
* [Flask 文档]
* [Azure 存储]
* [Azure SDK for Python]
* [如何从 Python 使用表存储服务]

## <a name="whats-changed"></a>发生的更改
* 有关从网站更改为应用服务的指南，请参阅 [Azure 应用服务及其对现有 Azure 服务的影响](http://go.microsoft.com/fwlink/?LinkId=529714)

<!--Link references-->
[Python 开发人员中心]: /develop/python/
[Azure 云服务]: ../cloud-services/cloud-services-python-ptvs.md
[文档]:../cosmos-db/table-storage-how-to-use-python.md
[如何从 Python 使用表存储服务]:../cosmos-db/table-storage-how-to-use-python.md

<!--External Link references-->
[Azure Portal]: https://portal.azure.com
[用于 .NET 的 Azure SDK]: http://azure.microsoft.com/downloads/
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 for Visual Studio 示例 VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK Tools for VS 2015]: http://go.microsoft.com/fwlink/?linkid=518003
[Python 2.7（32 位）]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4（32 位）]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools for Visual Studio 文档]: http://aka.ms/ptvsdocs
[Flask 文档]: http://flask.pocoo.org/
[在 Microsoft Azure 上进行远程调试]: http://go.microsoft.com/fwlink/?LinkId=624026
[Web 项目]: http://go.microsoft.com/fwlink/?LinkId=624027
[云服务项目]: http://go.microsoft.com/fwlink/?LinkId=624028
[Azure 存储]: http://azure.microsoft.com/documentation/services/storage/
[Azure SDK for Python]: https://github.com/Azure/azure-sdk-for-python


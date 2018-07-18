---
title: 如何通过 Visual Studio 将 Web 应用程序迁移和发布到 Azure 云服务 | Microsoft Docs
description: 了解如何使用 Visual Studio 将 Web 应用程序迁移和发布到 Azure 云服务
services: visual-studio-online
author: ghogen
manager: douge
ms.assetid: 9394adfd-a645-4664-9354-dd5df08e8c91
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ghogen
ms.openlocfilehash: aa09cd06a5ccea3f18459efb701aeaa8d9e59639
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796204"
---
# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>如何：通过 Visual Studio 将 Web 应用程序迁移和发布到 Azure 云服务

要利用 Azure 的托管服务和缩放功能，可能需要将 Web 应用程序迁移和部署到 Azure 云服务。 只需要很小的更改。 本文仅介绍了如何部署到云服务；对于应用服务，请参阅[在 Azure 应用服务中部署 Web 应用](app-service/app-service-deploy-local-git.md)。

> [!Important]
> 仅特定的 ASP.NET、Silverlight、WCF 和 WCF 工作流项目支持此迁移， 而 ASP.NET Core 项目不支持。 请参阅[支持的项目模板](#supported-project-templates)。

## <a name="migrate-a-project-to-cloud-services"></a>将项目迁移到云服务

1. 右键单击 Web 应用程序项目，然后选择“转换”>“转换到 Microsoft Azure 云服务项目”。 （请注意，如果解决方案中已有 web 角色项目，则不会显示此命令。）
1. Visual Studio 在包含所需 Web 角色的解决方案中创建一个云服务项目。 该项目的名称与加上后缀 `.Azure` 的应用程序项目相同。
1. 对于 MVC 2、MVC 3、MVC 4 和 Silverlight 业务应用程序需要的任何程序集，Visual Studio 还将“复制本地”属性设置为 true。 此属性将这些程序集添加到用于部署的服务包。

   > [!Important]
   > 如果有此 Web 应用程序所需的其他程序集或文件，必须手动设置这些文件的属性。 有关如何设置这些属性的信息，请参阅[在服务包中包含文件](#include-files-in-the-service-package)。

### <a name="errors-and-warnings"></a>错误和警告

发生的任何警告或错误都会指示在部署到 Azure 之前要解决的问题，例如缺少程序集。

如果构建应用程序，请使用计算模拟器在本地运行它，或将其发布到 Azure，你可能会看到错误：“指定的路径、文件名太长，或两者都太长。” 此错误表示完全限定的 Azure 项目名称的长度超过了 146 个字符。 要解决该问题，请将你的解决方案移到路径较短的其他文件夹中。

有关如何将任何警告视为错误的详细信息，请参阅[使用 Visual Studio 配置 Azure 云服务项目](vs-azure-tools-configuring-an-azure-project.md)。

### <a name="test-the-migration-locally"></a>在本地测试迁移

1. 在 Visual Studio 的“解决方案资源管理器”中，右键单击添加的云服务项目，然后选择“设为启动项目”。
1. 选择“调试 > 启动调试” (F5) 以启动 Azure 调试环境。 该环境专门提供了各种 Azure 服务的仿真。

### <a name="use-an-azure-sql-database-for-your-application"></a>为你的应用程序使用 Azure SQL 数据库

如果你的 Web 应用程序具有使用本地 SQL Server 数据库的连接字符串，则必须将数据库迁移到 Azure SQL 数据库，然后更新连接字符串。 有关此过程的指导，请参阅以下主题：

- [将 SQL Server 数据库迁移到云中的 SQL 数据库](sql-database/sql-database-cloud-migrate.md)
- [使用 .NET (C#) 和 Visual Studio 连接并查询 Azure SQL 数据库](sql-database/sql-database-connect-query-dotnet-visual-studio.md)。

## <a name="publish-the-application-to-azure-cloud-service"></a>将应用程序发布到 Azure 云服务

1. 如[准备从 Visual Studio 发布或部署 Azure 应用程序](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)中所述，在 Azure 订阅中创建必需的云服务和存储帐户。
1. 在 Visual Studio 中，右键单击应用程序项目，然后选择“发布到 Microsoft Azure...”（与“发布...”命令不同）。
1. 在出现的“发布 Azure 应用程序”中，使用你的 Azure 订阅帐户登录，然后选择“下一步 >”。
1. 在“设置 > 通用设置”选项卡中，从“云服务”下拉列表中选择目标云服务以及你选择的环境和配置。 
1. 在“设置 > 高级设置”中，选择要使用的存储帐户，然后选择“下一步 >”。
1. 在“诊断”中，选择是否将信息发送至 Application Insights。
1. 选择“下一步 >”以查看摘要，然后选择“发布”以开始部署。
1. Visual Studio 将打开一个活动日志窗口，你可以在其中跟踪进度：

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. （可选）要取消部署过程，请右键单击活动日志中的行项目，然后选择“取消并删除”。 此命令会停止部署过程，并从 Azure 中删除部署环境。 注意：要在部署后删除这个部署环境，必须使用 [Azure门户](https://portal.azure.com)。
1. （可选）角色实例启动后，Visual Studio 会自动在“服务器资源管理器 > 云服务”节点中显示部署环境。 可以从此位置查看单个角色实例的状态。
1. 要在部署后访问你的应用程序，当 Azure 活动日志中显示“已完成”状态以及 URL 时，请选择部署旁边的箭头。 有关如何从 Azure 启动特定类型的 Web 应用程序的详细信息，请参阅下表。

## <a name="using-the-compute-emulator-and-starting-application-in-azure"></a>在 Azure 中使用计算模拟器并启动应用程序

通过选择“调试 > 启动调试”(F5)，可以在连接到 Visual Studio 调试程序的浏览器中启动所有应用程序类型。 通过 ASP.NET 的空白 Web 应用程序项目，必须先在应用程序中添加一个 `.aspx` 页面，并将其设置为 Web 项目的起始页。

下表提供了有关在 Azure 中启动应用程序的详细信息：

   | Web 应用程序类型 | 在 Azure 中运行 |
   | --- | --- | --- |
   | ASP.NET Web 应用程序<br/>（包括 MVC 2、MVC 3、MVC 4） | 选择 Azure 活动日志的“部署”选项卡中的 URL。 |
   | ASP.NET 空 Web 应用程序 | 如果你的应用程序中有默认的 `.aspx` 页面，请选择 Azure 活动日志的“部署”选项卡中的 URL。 要导航到其他页面，请在浏览器中输入以下窗体的 URL：`<deployment_url>/<page_name>.aspx` |
   | Silverlight 应用程序<br/>Silverlight 业务应用程序<br/>Silverlight 导航应用程序 | 使用以下 URL 窗体导航到应用程序的特定页面：`<deployment_url>/<page_name>.aspx` |
    WCF 服务应用程序<br/>WCF 工作流服务应用程序 | 将 `.svc` 文件设置为 WCF 服务项目的起始页。 然后导航到 `<deployment_url>/<service_file>.svc`。 |
   | ASP.NET 动态实体<br/>ASP.NET 动态数据 Linq to SQL | 如下一部分所述更新连接字符串。 然后导航到 `<deployment_url>/<page_name>.aspx`。 对于 Linq to SQL，你必须使用 Azure SQL 数据库。 |

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>为 ASP.NET 动态实体更新连接字符串

1. 如前面 (#use-an-azuresql-database-for-your-application) 中所述，为 ASP.NET 动态实体 Web 应用程序创建 SQL Azure 数据库。
1. 从 Azure 门户添加需要用于此数据库的表和字段。
1. 使用以下格式在 `web.config` 文件中指定连接字符串并保存该文件：

    ```xml
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    使用 SQL Azure 数据库的 ADO.NET 连接字符串更新 *connectionString* 值，如下所示：

    ```xml
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

## <a name="supported-project-templates"></a>支持的项目模板

可以迁移并发布到云服务的应用程序必须使用下表中的一个模板。 不支持 ASP.NET Core。

| 模板组 | 项目模板 |
| --- | --- |
| Web | ASP.NET Web 应用程序 (.NET Framework) |
| Web | ASP.NET MVC 2 Web 应用程序 |
| Web | ASP.NET MVC 3 Web 应用程序 |
| Web | ASP.NET MVC 4 Web 应用程序 |
| Web | ASP.NET 空白 Web 应用程序（或站点） |
| Web | ASP.NET MVC 2 空 Web 应用程序 |
| Web | ASP.NET 动态数据实体 Web 应用程序 |
| Web | ASP.NET 动态数据实体 Linq to SQL Web 应用程序 |
| Silverlight | Silverlight 应用程序 |
| Silverlight | Silverlight 业务应用程序 |
| Silverlight | Silverlight 导航应用程序 |
| WCF | WCF 服务应用程序 |
| WCF | WCF 工作流服务应用程序 |
| 工作流 | WCF 工作流服务应用程序 |

## <a name="next-steps"></a>后续步骤

- [准备从 Visual Studio 发布或部署 Azure 应用程序](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)
- [设置命名的身份验证凭据](vs-azure-tools-setting-up-named-authentication-credentials.md)。

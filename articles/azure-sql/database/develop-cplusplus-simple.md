---
title: 使用 C 和 C++ 连接到 SQL 数据库
description: 使用本快速入门教程中的示例代码，生成一个包含 C++ 代码的新式应用程序，该应用程序由云中强大的关系数据库 Azure SQL 数据库提供支持。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: cpp
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: 38e4839a41ad8e58e575e552e877303a5105ff36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443672"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>使用 C 和 C++ 连接到 SQL 数据库
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文面向尝试连接到 Azure SQL 数据库的 C 和 C++ 开发人员。 它分为多个部分，方便大家选择最感兴趣的部分进行查看。

## <a name="prerequisites-for-the-cc-tutorial"></a>C/C++ 教程的先决条件

确保具有以下内容：

* 有效的 Azure 帐户。 如果没有，可以注册 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* [Visual Studio](https://www.visualstudio.com/downloads/)。 必须安装 C++ 语言组件才能生成和运行此示例。
* [Visual Studio Linux 开发](https://docs.microsoft.com/cpp/linux/?view=vs-2019)。 如果基于 Linux 开发，还必须安装 Visual Studio Linux 扩展。

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>Azure SQL 数据库和虚拟机上的 SQL Server

Azure SQL 数据库基于 Microsoft SQL Server 构建，旨在提供高可用性、高性能和可缩放的服务。 在本地运行的专有数据库中使用 Azure SQL 有很多好处。 使用 Azure SQL 时，需要安装、设置、维护或管理的不是数据库，而是数据库的内容和结构。 我们通常担心的数据库容错和冗余等功能全都内置其中。

Azure 目前有两个选项可用于托管 SQL Server 工作负载：Azure SQL 数据库（数据库即服务）和虚拟机 (VM) 上的 SQL Server。 我们不会详细介绍两者之间的差异，但需要了解的是，如果想要利用云服务提供的成本节省和性能优化，那么 Azure SQL 数据库是使用新的基于云的应用程序的最佳选择。 如果正在考虑将本地应用程序迁移或扩展到云中，Azure 虚拟机上的 SQL Server 可能更适合。 为了使本文简单明了，让我们创建一个 Azure SQL 数据库。

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>数据访问技术：ODBC 和 OLE DB

连接到 Azure SQL 数据库没有任何差别，目前可以通过两种方法连接到数据库：ODBC（开放式数据库连接）和 OLE DB（对象链接与嵌入数据库）。 最近几年，Microsoft 已在使用 [ODBC 进行本地关系数据访问](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/)。 ODBC 相对简单，并且比 OLE DB 快得多。 唯一需要说明的是，ODBC 使用的是旧的 C 样式 API。

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>步骤 1：创建 Azure SQL 数据库

请参阅[入门页](single-database-create-quickstart.md)，以了解如何创建示例数据库。  或者，你可以遵循这 [一短两分钟的视频](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) ，通过 Azure 门户创建 Azure SQL 数据库。

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>步骤 2：获取连接字符串

预配 Azure SQL 数据库后，需要执行以下步骤，确定连接信息及添加用于防火墙访问的客户端 IP。

在 [Azure 门户](https://portal.azure.com/)中，使用“显示数据库连接字符串”（包含在数据库概述部分中）转到 Azure SQL 数据库 ODBC 连接字符串：

![ODBCConnectionString](./media/develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/develop-cplusplus-simple/dbconnection.png)

复制 **ODBC (包括 Node.js) [SQL 身份验证]** 字符串的内容。 稍后，我们使用此字符串从 C++ ODBC 命令行解释程序进行连接。 此字符串提供驱动程序、服务器和其他数据库连接参数等详细信息。

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>步骤 3：将 IP 添加到防火墙

转到服务器的防火墙部分，并[使用以下步骤将客户端 IP 添加到防火墙](firewall-configure.md)，以确保我们可以建立成功的连接：

![AddyourIPWindow](./media/develop-cplusplus-simple/ip.png)

此时，已配置好 Azure SQL 数据库，并已准备好通过 C++ 代码连接。

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>步骤 4：从 Windows C/C++ 应用程序连接

可以[使用通过 Visual Studio 生成的此示例在 Windows 上轻松连接到使用 ODBC 的 Azure SQL 数据库](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29)。 该示例实现可用于连接到 Azure SQL 数据库的 ODBC 命令行解释器。 此示例将数据库源名称文件 (DSN) 文件作为命令行参数，或我们先前从 Azure 门户复制的详细的连接字符串。 打开此项目的属性页，并将连接字符串作为命令行参数粘贴，如下所示：

![DSN Propsfile](./media/develop-cplusplus-simple/props.png)

确保在该数据库连接字符串中为数据库提供正确的身份验证详细信息。

启动用于生成的应用程序。 应看到如下所示确认成功连接的窗口。 甚至可以运行一些基本的 SQL 命令（例如 **create table** ）来验证数据库连接：

![SQL 命令](./media/develop-cplusplus-simple/sqlcommands.png)

或者，可以使用未提供命令行参数时启动的向导创建 DSN 文件。 我们也建议尝试此选项。 可以使用此 DSN 文件进行自动化以及保护身份验证设置：

![创建 DSN 文件](./media/develop-cplusplus-simple/datasource.png)

祝贺！ 现在已成功在 Windows 上使用 C++和 ODBC 连接到 Azure SQL。 可以继续阅读如何为 Linux 平台执行相同操作的内容。

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>步骤 5：从 Linux C/C++ 应用程序连接

也许你还不知道，Visual Studio 现在也允许开发 C++ Linux 应用程序。 可以在 [Visual C++ for Linux Development](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) （用于 Linux 开发的 Visual C++）博客中阅读关于此新方案的信息。 若要为 Linux 生成，需要运行 Linux 分发的远程计算机。 如果没有可用的远程计算机，可以使用 [Linux Azure 虚拟机](../../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)快速设置。

对于本教程，我们假设已设置好 Ubuntu 16.04 Linux 分发。 此处的步骤还适用于 Ubuntu 15.10、Red Hat 6 和 Red Hat 7。

按照以下步骤安装发行版 SQL 和 ODBC 所需的库：

```console
    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*
```

启动 Visual Studio。 在“工具”->“选项”->“跨平台”->“连接管理器” 下，添加到 Linux 框的连接：

![工具选项](./media/develop-cplusplus-simple/tools.png)

建立通过 SSH 的连接后，创建一个空项目 (Linux) 模板：

![新建项目模板](./media/develop-cplusplus-simple/template.png)

然后，可以添加 [ 新的 C 源文件，并将其替换为此内容](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c)。 使用 ODBC API SQLAllocHandle、SQLSetConnectAttr 和 SQLDriverConnect 时，应能够初始化并建立与数据库的连接。
和 Windows ODBC 示例一样，需要使用数据库连接字符串参数的详细信息（之前从 Azure 门户复制）替换 SQLDriverConnect 调用。

```c
     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);
```

编译前需要完成的最后一步是将 **odbc** 作为库依赖项添加：

![将 ODBC 作为输入库添加](./media/develop-cplusplus-simple/lib.png)

若要启动应用程序，请从“调试”  菜单打开 Linux 控制台：

![Linux 控制台](./media/develop-cplusplus-simple/linuxconsole.png)

如果已成功连接，现在应看到 Linux 控制台中显示当前数据库名称：

![Linux 控制台窗口输出](./media/develop-cplusplus-simple/linuxconsolewindow.png)

祝贺！ 你已成功完成本教程，现在可以在 Windows 和 Linux 平台上通过 C++ 连接到 Azure SQL 数据库。

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>获取完整的 C/C++ 教程解决方案

可以在 GitHub 中查找包括本文所有示例的 GetStarted 解决方案：

* [ODBC C++ Windows 示例](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29)，下载 Windows C++ ODBC 示例，连接到 Azure SQL
* [ODBC C++ Linux 示例](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29)，下载 Linux C++ ODBC 示例，连接到 Azure SQL

## <a name="next-steps"></a>后续步骤

* 参阅 [SQL 数据库开发概述](develop-overview.md)
* [ODBC API 参考](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>其他资源

* [具有 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](saas-tenancy-app-design-patterns.md)
* 浏览所有 [SQL 数据库的功能](https://azure.microsoft.com/services/sql-database/)。

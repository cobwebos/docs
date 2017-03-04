---
title: "使用 C 和 C++ 连接到 SQL 数据库 | Microsoft 文档"
description: "使用本快速入门教程中的示例代码可以生成一个包含 C++ 代码并由云中强大的 Azure SQL 数据库关系数据库支持的现代应用程序。"
services: sql-database
documentationcenter: 
author: asthana86
manager: danmoth
editor: 
ms.assetid: 07d9e0b1-3234-4f17-a252-a7559160a9db
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: article
ms.date: 02/03/2017
ms.author: tobiast
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: 697a99ec828984d4e6f6e3dc446bc6dc8377cf57
ms.lasthandoff: 02/07/2017


---
# <a name="connect-to-sql-database-using-c-and-c"></a>使用 C 和 C++ 连接到 SQL 数据库
本帖子适用于尝试接到 Azure SQL DB 的 C 和 C++ 开发人员。 它分为多个部分，你可以跳转到最感兴趣的部分。 

## <a name="prerequisites-for-the-cc-tutorial"></a>C/C++ 教程的先决条件
请确保你具有以下项：

* 有效的 Azure 帐户。 如果你没有，可以注册 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* [Visual Studio](https://www.visualstudio.com/downloads/)。 必须安装 C++ 语言组件才能构建并运行此示例。
* [Visual Studio Linux 开发](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e)。 如果在 Linux 上进行开发，则还必须安装 Visual Studio Linux 扩展。 

## <a name="a-idazuresqlaazure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>Azure SQL 数据库和虚拟机上的 SQL Server
Azure SQL 构建在 Microsoft SQL Server 上，旨在提供高可用性、高性能和可缩放的服务。 在本地运行的专有数据库上使用 SQL Azure 有很多优势。 有了 SQL Azure，除数据库的内容和结构外，无需对数据库进行安装、设置、维护或管理。 数据库中内置了容错和冗余等，这些都是我们担心的典型问题。 

Azure 当前有两个托管 SQL Server 工作负荷的选项：Azure SQL 数据库（作为服务）和虚拟机 (VM) 上的 SQL Server。 我们不会详细介绍两者的差异，但对于基于云的新应用程序而言，Azure SQL 数据库会是你的最佳匹配，藉此可利用云服务提供的成本节约和性能优化。 如果你正在考虑将本地应用程序迁移或扩展到云，Azure 虚拟机上的 SQL Server 可能更适合你。 为了本文的简单起见，让我们创建一个 Azure SQL 数据库。 

## <a name="a-idodbcadata-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>数据访问技术：ODBC 和 OLE DB
连接到 Azure SQL DB 没有任何不同，且当前有两种方法连接到数据库：ODBC（开放数据库连接）和 OLE DB（对象链接和嵌入数据库）。 最近几年，Microsoft 已在使用 [ODBC 进行本地关系数据访问](https://blogs.msdn.microsoft.com/sqlnativeclient/2011/08/29/microsoft-is-aligning-with-odbc-for-native-relational-data-access/)。 ODBC 相对简单，且比 OLE DB 快得多。 唯一需要说明的是，ODBC 使用的是旧的 C 样式 API。 

## <a name="a-idcreateastep-1--creating-your-azure-sql-database"></a><a id="Create"></a>步骤 1：创建 Azure SQL 数据库
请参阅[入门页](sql-database-get-started.md)，以了解如何创建示例数据库。  或者，可以遵循此[两分钟短视频](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/)使用 Azure 门户创建 Azure SQL 数据库。

## <a name="a-idconnectionstringastep-2--get-connection-string"></a><a id="ConnectionString"></a>步骤 2：获取连接字符串
设置 Azure SQL 数据库后，需要执行以下步骤来确定连接信息，并添加客户端 IP 用于防火墙访问。 

在 [Azure 门户](https://portal.azure.com/)中，使用**显示数据库连接字符串**（包含在数据库概述部分中）转到 Azure SQL 数据库 ODBC 连接字符串： 

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

复制 **ODBC (包括 Node.js) [SQL 身份验证]** 字符串的内容。 稍后，我们将使用此字符串从 C++ ODBC 命令行解释程序进行连接。 此字符串提供驱动程序、服务器和其他数据库连接参数等详细信息。 

## <a name="a-idfirewallastep-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>步骤 3：将 IP 添加到防火墙
转到数据库服务器的防火墙部分，并[使用以下步骤将客户端 IP 添加到防火墙](sql-database-configure-firewall-settings.md)，以确保我们可以建立成功的连接： 

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

此时，已配置了 Azure SQL DB 并准备从 C++ 代码连接。 

## <a name="a-idwindowsastep-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>步骤 4：从 Windows C/C++ 应用程序连接
可以使用用 Visual Studio 构建的示例[在 Windows 上使用 ODBC 连接Azure SQL DB](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29)。 此示例实现 ODBC 命令行解释程序，其可用来连接到我们的 Azure SQL DB。 此示例将数据库源名称文件 (DSN) 文件作为命令行参数，或我们先前从 Azure 门户复制的详细的连接字符串。 打开此项目的属性页，将连接字符串粘贴为命令参数，如下所示： 

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

请确保提供正确的数据库身份验证详情，作为数据库连接字符串的一部分。 

启动应用程序以进行构建。 应该看到以下窗口正在验证成功的连接。 甚至可以运行一些基本的 SQL 命令（如**创建表**来验证数据库连接性：

![SQL 命令](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

或者，可以使用不提供命令参数时启动的向导来创建 DSN 文件。 我们也建议你尝试此选项。 可以使用此 DSN 文件进行自动化并保护身份验证设置： 

![创建 DSN 文件](./media/sql-database-develop-cplusplus-simple/datasource.png)

祝贺你！ 现在已成功使用 Windows 上的 C++ 和 ODBC 连接到 Azure SQL。 也可以继续阅读以对 Linux 平台进行相同的操作。 

## <a name="a-idlinuxastep-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>步骤 5：从 Linux C/C++ 应用程序连接
也许你还不知道，Visual Studio 现在也允许开发 C++ Linux 应用程序。 可以在 [Visual C++ for Linux Development](https://blogs.msdn.microsoft.com/vcblog/2016/03/30/visual-c-for-linux-development/)（用于 Linux 开发的 Visual C++）博客中阅读此新方案。 要针对 Linux 构建，将需要一台运行 Linux 发行版的远程计算机。 如果没有可用的远程计算机，可以使用 [Linux Azure 虚拟机](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)快速设置。 

对于本教程，我们假设你设置了 Ubuntu 16.04 Linux 发行版。 此处的步骤也适用于 Ubuntu 15.10、 Red Hat 6 和 Red Hat 7。 

按照以下步骤安装你的发行版 SQL 和 ODBC 所需的库：

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

启动 Visual Studio 2015。 在工具 -> 选项 -> 交叉平台 -> C++ -> 连接管理器下，添加到 Linux 盒子的连接： 

![工具选项](./media/sql-database-develop-cplusplus-simple/tools.png)

建立通过 SSH 的连接后，创建一个空项目 (Linux) 模板： 

![新建项目模板](./media/sql-database-develop-cplusplus-simple/template.png)

然后，可以添加[新 C 源文件，并将其替换为此内容](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c)。 使用 ODBC APIs SQLAllocHandle、SQLSetConnectAttr 和 SQLDriverConnect，应可以初始化并建立到数据库的连接。 与使用 Windows ODBC 示例类似，需要将 SQLDriverConnect 调用替换为先前从 Azure 门户复制的数据库连接字符串参数的详细信息。 

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

编译之前的最后一步是将 **odbc** 添加为库依赖项： 

![将 ODBC 添加为输入库](./media/sql-database-develop-cplusplus-simple/lib.png)

要启动应用程序，从“**调试**”菜单中打开 Linux 控制台： 

![Linux 控制台](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

如果已成功连接，现在应看到在 Linux 控制台中打印的当前数据库名称： 

![Linux 控制台窗口输出](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

祝贺你！ 已成功完成本教程，现在可以从 Windows 和 Linux 平台上的 C++ 连接到 Azure SQL DB。

## <a name="a-idgetsolutionaget-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>获取完整的 C/C++ 教程解决方案
可以在 github 中查找包括本文所有示例的 GetStarted 解决方案：

* [ODBC C++ Windows 示例](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29)，下载 Windows C++ ODBC 示例以连接到 Azure SQL
* [ODBC C++ Windows 示例](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29)，下载 Linux C++ ODBC 示例以连接到 Azure SQL

## <a name="next-steps"></a>后续步骤
* 参阅 [SQL 数据库开发概述](sql-database-develop-overview.md)
* 更多有关 [ODBC API 引用](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)的信息

## <a name="additional-resources"></a>其他资源
* [包含 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 浏览所有 [SQL 数据库的功能](https://azure.microsoft.com/services/sql-database/)。



---
title: 导入或导出 Azure SQL 数据库但不允许 Azure 服务访问服务器。
description: 导入或导出 Azure SQL 数据库但不允许 Azure 服务访问服务器。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 9b34a2435486a905923e783153ccae97628193a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443752"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>导入或导出 Azure SQL 数据库但不允许 Azure 服务访问服务器
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文介绍如何在服务器上将“允许 Azure 服务”设置为“关闭”的情况下导入或导出数据库 。 工作流使用 Azure 虚拟机运行 SqlPackage 来执行导入或导出操作。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-the-azure-virtual-machine"></a>创建 Azure 虚拟机

通过选择“部署到 Azure”按钮来创建 Azure 虚拟机。

借助此模板，可以使用 Windows 版本（最新补丁版本）适用的其他一些选项来部署简单的 Windows 虚拟机。 这会在资源组位置部署 A2 大小 VM，并返回 VM 的完全限定的域名。
<br><br>

[![图像显示标记为“部署到 Azure”的按钮。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json)

有关详细信息，请参阅 [Windows VM 的极简部署](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)。

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机

以下步骤说明如何使用远程桌面连接连接到虚拟机。

1. 部署完成后，转到虚拟机资源。

   ![屏幕截图显示 "虚拟机概述" 页和 "连接" 按钮。](./media/database-import-export-azure-services-off/vm.png)  

2. 选择“连接” 。

   此时会显示远程桌面协议文件（.rdp 文件）窗体，其中包含虚拟机的公共 IP 地址和端口号。

   ![RDP 窗体](./media/database-import-export-azure-services-off/rdp.png)  

3. 选择“下载 RDP 文件”。

   > [!NOTE]
   > 也可使用 SSH 连接到 VM。

4. 关闭“连接到虚拟机”窗体。
5. 若要连接到 VM，请打开下载的 RDP 文件。
6. 出现提示时，选择“连接”。 在 Mac 上，需要一个 RDP 客户端，例如 Mac 应用商店提供的这个[远程桌面客户端](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12)。

7. 输入在创建虚拟机时指定的用户名和密码，然后选择“确定”。

8. 你可能会在登录过程中收到证书警告。 选择“是”或“继续”以继续连接。 

## <a name="install-sqlpackage"></a>安装 SqlPackage

[下载并安装最新版本的 SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download)。

有关更多信息，请参阅 [SqlPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)。

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>创建防火墙规则以允许 VM 访问数据库

将虚拟机的公共 IP 地址添加到服务器的防火墙。

以下步骤针对虚拟机的公共 IP 地址创建服务器级 IP 防火墙规则，并启用从虚拟机的连接。

1. 在左侧菜单中选择“SQL 数据库”，然后在“SQL 数据库”页上选择你的数据库。  此时会打开数据库的 "概述" 页，其中显示了完全限定的服务器名称 (例如 **servername.database.windows.net**) 并提供了进一步配置的选项。

2. 请复制此完全限定的服务器名称，以便在连接到服务器及其数据库时使用。

   ![服务器名称](./media/database-import-export-azure-services-off/server-name.png)

3. 在工具栏上选择“设置服务器防火墙”。 此时会打开服务器的“防火墙设置”页面。

   ![服务器级别 IP 防火墙规则](./media/database-import-export-azure-services-off/server-firewall-rule.png)

4. 在工具栏上选择“添加客户端 IP”，将虚拟机的公共 IP 地址添加到新的服务器级 IP 防火墙规则。 服务器级 IP 防火墙规则可以针对单个 IP 地址或一系列 IP 地址打开端口 1433。

5. 选择“保存” 。 此时会针对虚拟机的公共 IP 地址创建服务器级 IP 防火墙规则，在服务器上打开端口 1433。

6. 关闭“防火墙设置”页。

## <a name="export-a-database-using-sqlpackage"></a>使用 SqlPackage 导出数据库

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令行实用工具导出 Azure SQL 数据库，请参阅[导出参数和属性](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties)。 SqlPackage 实用工具随附了最新版本的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 和 [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)；你也下载最新版本的 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download)。

我们建议在大多数生产环境中使用 SqlPackage 实用工具来实现缩放和提高性能。 如需 SQL Server 客户顾问团队编写的有关使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。

此示例演示如何通过 Active Directory 通用身份验证，使用 SqlPackage.exe 来导出数据库。 请将占位符替换为环境特定的值。

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-a-database-using-sqlpackage"></a>使用 SqlPackage 导入数据库

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令行实用程序导入 SQL Server 数据库，请参阅[导入参数和属性](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)。 SqlPackage 包含最新的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 和 [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)。 也下载最新版本的 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download)。

在大多数生产环境中，建议使用 SqlPackage 而不是 Azure 门户来实现缩放和性能。 有关 SQL Server 客户咨询团队使用 `BACPAC` 文件进行迁移的博客，请参阅[使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)。

以下 SqlPackage 命令将 AdventureWorks2017 数据库从本地存储导入到某个 Azure SQL 数据库。 它将创建名为 myMigratedDatabase 的新数据库，其中包含“高级”服务层级和 P6 服务目标。 根据你的环境更改这些值。

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> 若要从公司防火墙后连接到 Azure SQL 数据库，该防火墙必须打开端口 1433。

此示例演示如何通过 Active Directory 通用身份验证，使用 SqlPackage 来导入数据库。

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>性能注意事项

许多因素（例如数据形状）会影响导出速度，因此预期的速度不可预测。 SqlPackage 可能需要花费相当长的时间，尤其是针对大型数据库运行时。

若要获得最佳性能，可以尝试以下策略：

1. 确保数据库上没有运行其他工作负荷。 在导出之前创建一个副本可能是确保没有其他工作负荷运行的最佳解决方法。
2. 提高数据库服务级别目标 (SLO) 以更好地处理导出工作负荷（主要为读取 I/O）。 如果数据库当前位于 GP_Gen5_4，则使用“业务关键”层也许可为读取工作负荷提供帮助。
3. 确保有尤其适合大型表的聚集索引。
4. 虚拟机 (VM) 应与数据库位于同一区域，以帮助避免网络约束。
5. 在上传到 Blob 存储之前，应在 VM 中提供足够大小的 SSD 来生成临时项目。
6. VM 应采用足够高的核心和内存配置，可以处理特定的数据库。

## <a name="store-the-imported-or-exported-bacpac-file"></a>存储已导入或导出的 .BACPAC 文件

可将 .BACPAC 文件存储在 [Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) 或 [Azure 文件存储](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)中。

若要实现最佳性能，请使用 Azure 文件存储。 SqlPackage 在文件系统中运行，因此它可以直接访问 Azure 文件存储。

若要降低成本，请使用成本低于高级 Azure 文件共享的 Azure Blob。 但是，如果使用 Azure Blob，在执行导入或导出操作之前，需要在 Blob 与本地文件系统之间复制 [.BACPAC 文件](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac)。 因此，该过程需要更长的时间。

若要上传或下载 .BACPAC 文件，请参阅[使用 AzCopy 和 Blob 存储传输数据](../../storage/common/storage-use-azcopy-blobs.md)以及[使用 AzCopy 和文件存储传输数据](../../storage/common/storage-use-azcopy-files.md)。

根据具体的环境，可能需要[配置 Azure 存储防火墙和虚拟网络](../../storage/common/storage-network-security.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何连接到导入的 SQL 数据库并进行查询，请参阅[快速入门：Azure SQL 数据库：使用 SQL Server Management Studio 进行连接和数据查询](connect-query-ssms.md)。
- 如需 SQL Server 客户顾问团队编写的有关使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。
- 有关对于整个 SQL Server 数据库迁移进程（包括性能建议）的讨论，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](migrate-to-database-from-sql-server.md)。
- 若要了解如何安全地管理和共享存储密钥和共享访问签名，请参阅 [Azure 存储安全指南](https://docs.microsoft.com/azure/storage/common/storage-security-guide)。

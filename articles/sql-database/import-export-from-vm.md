---
title: 导入或导出 SQL 数据库
description: 导入或导出 Azure SQL 数据库，但不允许 Azure 服务访问该服务器。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 9f694f3f0ec740d0a4e8dc4e6bf8845c408802c8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897840"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>导入或导出 Azure SQL 数据库，但不允许 Azure 服务访问服务器

本文说明如何在 Azure SQL server 上将 "*允许 Azure 服务*" 设置为 "*关闭*" 时导入或导出 azure sql 数据库。 工作流使用 Azure 虚拟机来运行 SqlPackage，以执行导入或导出操作。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="create-the-azure-virtual-machine"></a>创建 Azure 虚拟机

通过选择 "**部署到 azure** " 按钮，创建 Azure 虚拟机。

此模板允许你使用最新的修补版本，为 Windows 版本使用几个不同的选项部署简单的 Windows 虚拟机。 这会在资源组位置中部署 A2 大小 VM，并返回 VM 的完全限定域名。
<br><br>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

有关详细信息，请参阅[WINDOWS VM 的非常简单的部署](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)。


## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机

以下步骤演示如何使用远程桌面连接来连接到你的虚拟机。

1. 部署完成后，转到虚拟机资源。

    ![VM](./media/import-export-from-vm/vm.png)  

2. 选择“连接”。

   此时会显示远程桌面协议文件（.rdp 文件）窗体，其中包含虚拟机的公共 IP 地址和端口号。

   ![RDP 窗体](./media/import-export-from-vm/rdp.png)  

3. 选择“下载 RDP 文件”。

   > [!NOTE]
   > 也可使用 SSH 连接到 VM。

4. 关闭“连接到虚拟机”窗体。
5. 若要连接到 VM，请打开下载的 RDP 文件。
6. 出现提示时，选择“连接”。 在 Mac 上，需要一个 RDP 客户端，例如 Mac 应用商店提供的这个[远程桌面客户端](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)。

7. 输入在创建虚拟机时指定的用户名和密码，然后选择“确定”。

8. 你可能会在登录过程中收到证书警告。 选择“是”或“继续”以继续连接。



## <a name="install-sqlpackage"></a>安装 SqlPackage

[下载并安装最新版本的 SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download)。




有关其他信息，请参阅[SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage)。

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>创建防火墙规则以允许 VM 访问数据库

将虚拟机的公共 IP 地址添加到 SQL 数据库服务器防火墙。

以下步骤将为虚拟机的公共 IP 地址创建服务器级 IP 防火墙规则，并从虚拟机中启用连接。

1. 从左侧菜单中选择 " **sql 数据库**"，然后在 " **SQL 数据库**" 页上选择您的数据库。 此时会打开数据库的 "概述" 页，其中显示了完全限定的服务器名称（例如**servername.database.windows.net**），并提供了进一步配置的选项。

2. 复制此完全限定的服务器名称，以便在连接到服务器及其数据库时使用。

   ![服务器名称](./media/sql-database-get-started-portal/server-name.png)

3. 在工具栏上选择“设置服务器防火墙”。 此时会打开数据库服务器的“防火墙设置”页。

   ![服务器级别 IP 防火墙规则](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. 选择工具栏上的 "**添加客户端 IP** "，将虚拟机的公共 ip 地址添加到新的服务器级别 ip 防火墙规则。 服务器级 IP 防火墙规则可以针对单个 IP 地址或一系列 IP 地址打开端口 1433。

5. 选择“保存”。 为虚拟机的公共 IP 地址创建服务器级 IP 防火墙规则，该规则将在 SQL 数据库服务器上打开端口1433。

6. 关闭“防火墙设置”页。



## <a name="export-a-database-using-sqlpackage"></a>使用 SqlPackage 导出数据库

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令行实用工具导出 SQL 数据库，请参阅[导出参数和属性](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties)。 SqlPackage 实用程序附带最新版本的[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)和[SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)，你也可以下载最新版本的[SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download)。

在大多数生产环境中，我们建议使用 SqlPackage 实用工具来实现缩放和性能。 有关 SQL Server 客户咨询团队使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。

此示例演示如何使用 SqlPackage 通过 Active Directory 通用身份验证导出数据库。 替换为特定于您的环境的值。

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```




## <a name="import-a-database-using-sqlpackage"></a>使用 SqlPackage 导入数据库

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令行实用程序导入 SQL Server 数据库，请参阅[导入参数和属性](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)。 SqlPackage 具有最新的[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)和[SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)。 你还可以下载最新版本的[SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download)。

在大多数生产环境中，建议使用 SqlPackage 而不是 Azure 门户来实现缩放和性能。 有关 SQL Server 客户咨询团队使用 `BACPAC` 文件进行迁移的博客，请参阅[使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)。

以下 SqlPackage 命令将**AdventureWorks2017**数据库从本地存储导入到 Azure SQL 数据库服务器。 它将创建名为 myMigratedDatabase 的新数据库，其中包含“高级”服务层级和 P6 服务目标。 根据你的环境更改这些值。

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> 若要从公司防火墙后连接到管理单一数据库的 SQL 数据库服务器，该防火墙必须打开端口 1433。 若要连接到托管实例，必须有[点到站点连接](sql-database-managed-instance-configure-p2s.md)或快速路由连接。

此示例演示如何通过 Active Directory 通用身份验证，使用 SqlPackage 来导入数据库。

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>性能考虑

由于许多因素（例如，数据形状），导出速度会有所不同，因此无法预测预计的速度。 SqlPackage 可能需要相当长的时间，尤其是对于大型数据库。

若要获得最佳性能，可以尝试以下策略：

1. 请确保数据库上没有运行任何其他工作负荷。 在导出之前创建副本可能是确保没有其他工作负荷运行的最佳解决方案。
2. 提高数据库服务级别目标（SLO），以更好地处理导出工作负荷（主要是读取 i/o）。 如果当前正在 GP_Gen5_4 数据库，则可能是业务关键层有助于读取工作负荷。
3. 请确保存在专用于大表的聚集索引。 
4. 虚拟机（Vm）应与数据库位于同一区域，以帮助避免网络约束。
5. 在上传到 blob 存储之前，Vm 应具有足够大小的 SSD 来生成临时项目。
6. Vm 应为特定数据库提供充足的核心和内存配置。

## <a name="store-the-imported-or-exported-bacpac-file"></a>存储导入或导出的。BACPAC 文件

此.BACPAC 文件可以存储在[Azure blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)或[azure 文件](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)中。 

若要实现最佳性能，请使用 Azure 文件。 SqlPackage 操作文件系统，因此它可以直接访问 Azure 文件。

若要降低成本，请使用 Azure Blob，成本低于高级 Azure 文件共享。 但是，它将要求您复制[。](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac)导入或导出操作之前的 blob 和本地文件系统之间的 BACPAC 文件。 因此，此过程需要更长的时间。

要上传或下载。BACPAC 文件，请参阅[通过 AzCopy 和 Blob 存储传输数据](../storage/common/storage-use-azcopy-blobs.md)和[通过 AzCopy 和文件存储传输数据](../storage/common/storage-use-azcopy-files.md)。

根据你的环境，你可能需要[配置 Azure 存储防火墙和虚拟网络](../storage/common/storage-network-security.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何连接并查询导入的 SQL 数据库，请参阅[快速入门： AZURE SQL 数据库：使用 SQL Server Management Studio 来连接和查询数据](sql-database-connect-query-ssms.md)。
- 有关 SQL Server 客户咨询团队使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。
- 有关对于整个 SQL Server 数据库迁移进程（包括性能建议）的讨论，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](sql-database-single-database-migrate.md)。
- 若要了解如何安全地管理和共享存储密钥和共享访问签名，请参阅 [Azure 存储安全指南](https://docs.microsoft.com/azure/storage/common/storage-security-guide)。

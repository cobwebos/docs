---
title: 将 SQL Server 数据库备份到 Azure
description: 本文介绍如何将 SQL Server 备份到 Azure。 此外还介绍 SQL Server 的恢复。
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: e0a555125e50a974ae51a08d7870cdc3ec12fd39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84021086"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>关于 Azure VM 中的 SQL Server 备份

[Azure 备份](backup-overview.md)提供了一种基于流的专用解决方案，可备份在 Azure vm 中运行的 SQL Server。 此解决方案与 Azure 备份的零基础结构备份、长期保留和集中管理的优点保持一致。 此外，它还为 SQL Server 提供了以下优点：

1. 支持所有备份类型的工作负荷感知备份-完整备份、差异备份和日志记录
2. 15分钟 RPO （恢复点目标），频繁进行日志备份
3. 时点恢复最多一秒
4. 单个数据库级别的备份和还原

若要查看目前支持的备份和还原方案，请参阅[支持矩阵](sql-support-matrix.md#scenario-support)。

## <a name="backup-process"></a>备份过程

此解决方案利用 SQL 本机 API 来备份 SQL 数据库。

* 指定要保护的 SQL Server VM 并查询其中的数据库后，Azure 备份服务将在此 VM 上以 `AzureBackupWindowsWorkload` 扩展名安装工作负荷备份扩展。
* 此扩展包含协调器和 SQL 插件。 协调器负责触发多种操作（如配置备份、备份和还原）的工作流，插件负责实际数据流。
* 为了能够发现此 VM 上的数据库，Azure 备份将创建帐户 `NT SERVICE\AzureWLBackupPluginSvc`。 此帐户用于备份和还原，需要拥有 SQL sysadmin 权限。 `NT SERVICE\AzureWLBackupPluginSvc` 帐户是[虚拟服务帐户](https://docs.microsoft.com/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)，因此不需要任何密码管理。 Azure 备份利用 `NT AUTHORITY\SYSTEM` 帐户进行数据库发现/查询，因此此帐户需是 SQL 上的公共登录名。 如果 SQL Server VM 不是从 Azure 市场创建的，你可能会收到错误 **UserErrorSQLNoSysadminMembership**。 如果发生此错误，请[遵照这些说明](#set-vm-permissions)予以解决。
* 在所选数据库上触发配置保护后，备份服务将使用备份计划和其他策略详细信息设置协调器，扩展将这些详细信息本地缓存在 VM 上。
* 在计划的时间，协调器与插件通信，并开始使用 VDI 从 SQL 服务器流式处理备份数据。  
* 插件将数据直接发送到恢复服务保管库，因此不需要暂存位置。 Azure 备份服务在存储帐户中加密和存储数据。
* 数据传输完成后，协调器通过备份服务确认提交。

  ![SQL 备份体系结构](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>开始之前

在开始之前，请验证以下条件：

1. 确保有一个 SQL Server 实例在 Azure 中运行。 可以在市场中[快速创建 SQL Server 实例](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md)。
2. 查看[功能注意事项](sql-support-matrix.md#feature-consideration-and-limitations)和[方案支持](sql-support-matrix.md#scenario-support)。
3. 查看有关此方案的[常见问题解答](faq-backup-sql-server.md)。

## <a name="set-vm-permissions"></a>设置 VM 权限

  当你在 SQL Server 上运行发现时，Azure 备份会执行以下操作：

* 添加 AzureBackupWindowsWorkload 扩展。
* 创建 NT SERVICE\AzureWLBackupPluginSvc 帐户，以发现虚拟机上的数据库。 此帐户用于备份和还原，需要拥有 SQL sysadmin 权限。
* Azure 备份使用 NT AUTHORITY\SYSTEM 帐户来发现 VM 上运行的数据库。 此帐户必须是 SQL 上的公共登录名。

如果你未在 Azure 市场中创建 SQL Server VM，或者在 SQL 2008 和 2008 R2 上操作，可能会收到 **UserErrorSQLNoSysadminMembership** 错误。

有关在使用 Windows 2008 R2 上运行的 **SQL 2008** 和 **2008 R2** 时如何授予权限，请参阅[此文](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)。

对于所有其他版本，可使用以下步骤解决权限问题：

  1. 使用拥有 SQL Server sysadmin 权限的帐户登录到 SQL Server Management Studio (SSMS)。 除非需要特殊权限，否则 Windows 身份验证应该正常运行。
  2. 在 SQL Server 上，打开 "**安全/登录名**" 文件夹。

      ![打开“安全/登录名”文件夹以查看帐户](./media/backup-azure-sql-database/security-login-list.png)

  3. 右键单击 "**登录名**" 文件夹，然后选择 "**新建登录名**"。 在“登录名 - 新建”中，选择“搜索”。********

      ![在“登录名 - 新建”对话框中选择“搜索”](./media/backup-azure-sql-database/new-login-search.png)

  4. 在虚拟机注册和 SQL 发现阶段已创建 Windows 虚拟服务帐户 NT SSERVICE\AzureWLBackupPluginSvc****。 输入“输入要选择的对象名称”中显示的帐户名。**** 选择“检查名称”以解析名称。**** 单击“确定”。

      ![选择“检查名称”以解析未知的服务名称](./media/backup-azure-sql-database/check-name.png)

  5. 在“服务器角色”中，确保“sysadmin”角色已选中。******** 单击“确定”。 现在，所需的权限应会存在。

      ![确保 sysadmin 服务器角色已选中](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. 现在，将数据库与恢复服务保管库相关联。 在 Azure 门户上的“受保护的服务器”列表中，右键单击处于错误状态的服务器，并选择“重新发现数据库”********。

      ![验证服务器是否拥有相应的权限](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. 在“通知”区域查看进度。**** 找到选定的数据库后，会显示一条成功消息。

      ![部署成功消息](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> 如果 SQL Server 安装了多个 SQL Server 实例，则必须将 **NT Service\AzureWLBackupPluginSvc** 帐户的 sysadmin 权限添加到所有 SQL 实例。

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>为 SQL sysadmin 授予 SQL 2008 和 SQL 2008 R2 的权限

将 **NT AUTHORITY\SYSTEM** 和 **NT Service\AzureWLBackupPluginSvc** 登录名添加到 SQL Server 实例：

1. 在对象资源管理器中转到该 SQL Server 实例。
2. 导航到“安全性”->“登录名”
3. 右键单击“登录名”，然后单击“新建登录名...”**

    ![使用 SSMS 的新登录名](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. 转到“常规”选项卡并输入 **NT AUTHORITY\SYSTEM** 作为登录名。

    ![SSMS 的登录名](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. 转到“服务器角色”，选择“public”和“sysadmin”角色。******

    ![在 SSMS 中选择角色](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. 转到“状态”。** *授予*连接到数据库引擎的权限，并将“登录名”设置为“已启用”。**

    ![在 SSMS 中授予权限](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. 单击“确定”。
8. 重复相同的步骤序列（上述步骤 1-7），将 NT Service\AzureWLBackupPluginSvc 登录名添加到 SQL Server 实例。 如果该登录名已存在，请确保它具有 sysadmin 服务器角色并处于这种状态：已授予连接到数据库引擎的权限，且“登录名”设置为“已启用”。
9. 在授予权限后，在门户中重新**发现**数据库： **->** Azure VM 中的保管库备份基础结构 **->** 工作负荷：

    ![在 Azure 门户中重新发现数据库](media/backup-azure-sql-database/sql-rediscover-dbs.png)

或者，可以在管理员模式下运行以下 PowerShell 命令，来自动授予权限。 实例名称默认设置为 MSSQLSERVER。 根据需要更改脚本中的实例名称参数：

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>后续步骤

* [了解有关](backup-sql-server-database-azure-vms.md)备份 SQL Server 数据库的信息。
* [了解](restore-sql-database-azure-vm.md)如何还原已备份的 SQL Server 数据库。
* [了解](manage-monitor-sql-database-backup.md)如何管理已备份的 SQL Server 数据库。

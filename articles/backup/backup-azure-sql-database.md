---
title: 将 SQL Server 数据库备份到 Azure
description: 本文介绍如何将 SQL Server 备份到 Azure。 此外还介绍 SQL Server 的恢复。
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 7305a75852deac466028e6278fca76626d8c1820
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297469"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>关于 Azure VM 中的 SQL Server 备份

[Azure 备份](backup-overview.md)提供了一种基于流的专用解决方案，可备份在 Azure vm 中运行的 SQL Server。 此解决方案与 Azure 备份的零基础结构备份、长期保留和集中管理的优点保持一致。 此外，它还为 SQL Server 提供了以下优点：

1. 支持所有备份类型的工作负荷感知备份-完整备份、差异备份和日志记录
2. 15分钟 RPO （恢复点目标），频繁进行日志备份
3. 时点恢复最多一秒
4. 单个数据库级别的备份和还原

若要查看目前支持的备份和还原方案，请参阅[支持矩阵](backup-azure-sql-database.md#scenario-support)。

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

1. 确保有一个 SQL Server 实例在 Azure 中运行。 可以在市场中[快速创建 SQL Server 实例](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md)。
2. 查看[功能注意事项](#feature-consideration-and-limitations)和[方案支持](#scenario-support)。
3. 查看有关此方案的[常见问题解答](faq-backup-sql-server.md)。

## <a name="scenario-support"></a>方案支持

**支持** | **详细信息**
--- | ---
**支持的部署** | 支持 SQL 市场 Azure VM 和非市场（手动安装的 SQL Server）VM。
**支持的地理区域** | 澳大利亚东南部 (ASE)、东澳大利亚 (AE)、澳大利亚中部 (AC)、澳大利亚中部 2 (AC) <br> 巴西南部 (BRS)<br> 加拿大中部 (CNC)、加拿大东部 (CE)<br> 东南亚 (SEA)、东亚 (EA) <br> 美国东部 (EUS)、美国东部 2 (EUS2)、美国中西部 (WCUS)、美国西部 (WUS)；美国西部 2 (WUS 2) 美国中北部 (NCUS) 美国中部 (CUS) 美国中南部 (SCUS) <br> 印度中部 (INC)、印度南部 (INS)、印度西部 <br> 日本东部 (JPE)、日本西部 (JPW) <br> 韩国中部 (KRC)、韩国南部 (KRS) <br> 北欧 (NE)、西欧 <br> 英国南部 (UKS)、英国西部 (UKW) <br> US Gov 亚利桑那州、US Gov 弗吉尼亚州、US Gov 德克萨斯州、US DoD 中部、US DoD 东部 <br> 德国北部、德国中西部 <br> 瑞士北部、瑞士西部 <br> 法国中部 <br> 中国东部、中国东部 2、中国北部、中国北部 2
**受支持的操作系统** | Windows Server 2019、Windows Server 2016、Windows Server 2012、Windows Server 2008 R2 SP1 <br/><br/> 目前不支持 Linux。
**支持的 SQL Server 版本** | [搜索产品生命周期页](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)中详细介绍了 SQL Server 2019、SQL Server 2017，[搜索产品生命周期页](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)中详细介绍了 SQL Server 2016 和 SP、SQL Server 2014、SQL Server 2012、SQL Server 2008 R2、SQL Server 2008 <br/><br/> Enterprise、Standard、Web、Developer、Express。
**支持的 .NET 版本** | 安装在 VM 上的 .NET Framework 4.5.2 或更高版本

## <a name="feature-consideration-and-limitations"></a>功能注意事项和限制

* SQL Server 备份可配置在 Azure 门户或 PowerShell 中。 我们不支持 CLI。
* 此解决方案在 Azure 资源管理器 VM 和经典 VM 这两种[部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)上均受支持。
* 运行 SQL Server 的 VM 需要建立 Internet 连接才能访问 Azure 公共 IP 地址。
* 不支持 SQL Server**故障转移群集实例（FCI）** 。
* 不支持对镜像数据库和数据库快照执行备份和还原操作。
* 使用多个备份解决方案来备份独立的 SQL Server 实例或 SQL Always On 可用性组可能导致备份失败，请避免执行此操作。
* 如果通过相同或不同的解决方案单独备份可用性组的两个节点，可能也会导致备份失败。
* Azure 备份支持只读数据库的仅完整备份和仅复制完整备份类型
* 无法保护包含大量文件的数据库。 支持的最大文件数约为 1000。  
* 在一个保管库中最多可以备份约 2000 个 SQL Server 数据库。 如果有大量数据库，可创建多个保管库。
* 一次最多可配置 50 个数据库的备份；此限制有助于优化备份负载。
* 我们支持最大为**2 TB**的数据库;对于大于该性能问题的大小，可能会出现这种情况。
* 为了了解每个服务器可以保护的数据库数量，我们需要考虑带宽、VM 大小、备份频率、数据库大小等因素，并根据 VM 资源和备份策略，[下载](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx)提供每个服务器大概数量数据库的资源规划器。
* 对于可用性组，将基于几个因素从不同节点获取备份。 下面概述了可用性组的备份行为。

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Always On 可用性组的备份行为

建议只在 AG 的一个节点上配置备份。 应该始终在主节点所在的区域配置备份。 换句话说，必须始终确保主节点存在于进行备份配置的区域。 如果 AG 的所有节点都在配置了备份的同一区域，则无需考虑任何问题。

#### <a name="for-cross-region-ag"></a>对于跨区域 AG

* 无论备份首选项如何，都不会从配置了备份的同一区域中的节点进行备份。 这是因为跨区域备份不受支持。 如果只有两个节点，并且辅助节点在另一个区域中，则为;在这种情况下，备份将继续从主节点进行（除非您的备份首选项为 "仅辅助"）。
* 如果故障转移与备份配置不在同一区域进行，则已故障转移的区域中的节点上的备份会失败。

根据备份首选项和备份类型（完整/差异/日志/仅复制完整），从特定节点（主要/次要）获取备份。

* **备份首选项：主要**

**备份类型** | **Node**
    --- | ---
    完全 | 主
    差异 | 主
    日志 |  主
    仅复制完整 |  主

* **备份首选项：仅辅助副本**

**备份类型** | **Node**
--- | ---
完全 | 主
差异 | 主
日志 |  辅助副本
仅复制完整 |  辅助副本

* **备份首选项：辅助**

**备份类型** | **Node**
--- | ---
完全 | 主
差异 | 主
日志 |  辅助副本
仅复制完整 |  辅助副本

* **无备份首选项**

**备份类型** | **Node**
--- | ---
完全 | 主
差异 | 主
日志 |  辅助副本
仅复制完整 |  辅助副本

## <a name="set-vm-permissions"></a>设置 VM 权限

  当你在 SQL Server 上运行发现时，Azure 备份会执行以下操作：

* 添加 AzureBackupWindowsWorkload 扩展。
* 创建 NT SERVICE\AzureWLBackupPluginSvc 帐户，以发现虚拟机上的数据库。 此帐户用于备份和还原，需要拥有 SQL sysadmin 权限。
* Azure 备份使用 NT AUTHORITY\SYSTEM 帐户来发现 VM 上运行的数据库。 此帐户必须是 SQL 上的公共登录名。

如果你未在 Azure 市场中创建 SQL Server VM，或者在 SQL 2008 和 2008 R2 上操作，可能会收到 **UserErrorSQLNoSysadminMembership** 错误。

有关在使用 Windows 2008 R2 上运行的 **SQL 2008** 和 **2008 R2** 时如何授予权限，请参阅[此文](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)。

对于所有其他版本，可使用以下步骤解决权限问题：

  1. 使用拥有 SQL Server sysadmin 权限的帐户登录到 SQL Server Management Studio (SSMS)。 除非需要特殊权限，否则 Windows 身份验证应该正常运行。
  2. 在 SQL 服务器上，打开“安全/登录名”文件夹。

      ![打开“安全/登录名”文件夹以查看帐户](./media/backup-azure-sql-database/security-login-list.png)

  3. 右键单击“登录名”文件夹并选择“新建登录名”。 在“登录名 - 新建”中，选择“搜索”。

      ![在“登录名 - 新建”对话框中选择“搜索”](./media/backup-azure-sql-database/new-login-search.png)

  4. 在虚拟机注册和 SQL 发现阶段已创建 Windows 虚拟服务帐户 NT SSERVICE\AzureWLBackupPluginSvc。 输入“输入要选择的对象名称”中显示的帐户名。 选择“检查名称”以解析名称。 单击“确定”。

      ![选择“检查名称”以解析未知的服务名称](./media/backup-azure-sql-database/check-name.png)

  5. 在“服务器角色”中，确保“sysadmin”角色已选中。 单击“确定”。 现在，所需的权限应会存在。

      ![确保 sysadmin 服务器角色已选中](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. 现在，将数据库与恢复服务保管库相关联。 在 Azure 门户上的“受保护的服务器”列表中，右键单击处于错误状态的服务器，并选择“重新发现数据库”。

      ![验证服务器是否拥有相应的权限](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. 在“通知”区域查看进度。 找到选定的数据库后，会显示一条成功消息。

      ![部署成功消息](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> 如果 SQL Server 安装了多个 SQL Server 实例，则必须将 **NT Service\AzureWLBackupPluginSvc** 帐户的 sysadmin 权限添加到所有 SQL 实例。

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>为 SQL sysadmin 授予 SQL 2008 和 SQL 2008 R2 的权限

将 **NT AUTHORITY\SYSTEM** 和 **NT Service\AzureWLBackupPluginSvc** 登录名添加到 SQL Server 实例：

1. 在对象资源管理器中转到该 SQL Server 实例。
2. 导航到“安全性”->“登录名”
3. 右键单击“登录名”，然后单击“新建登录名...”

    ![使用 SSMS 的新登录名](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. 转到“常规”选项卡并输入 **NT AUTHORITY\SYSTEM** 作为登录名。

    ![SSMS 的登录名](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. 转到“服务器角色”，选择“public”和“sysadmin”角色。

    ![在 SSMS 中选择角色](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. 转到“状态”。 *授予*连接到数据库引擎的权限，并将“登录名”设置为“已启用”。

    ![在 SSMS 中授予权限](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. 单击“确定”。
8. 重复相同的步骤序列（上述步骤 1-7），将 NT Service\AzureWLBackupPluginSvc 登录名添加到 SQL Server 实例。 如果该登录名已存在，请确保它具有 sysadmin 服务器角色并处于这种状态：已授予连接到数据库引擎的权限，且“登录名”设置为“已启用”。
9. 在授予权限后，在门户中重新**发现**数据库：保管库 **->** 备份基础结构 **->** Azure VM 中的工作负荷：

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

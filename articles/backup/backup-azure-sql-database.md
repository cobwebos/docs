---
title: 将 SQL Server 数据库备份到 Azure | Microsoft Docs
description: 本教程介绍如何将 SQL Server 备份到 Azure， 此外还介绍 SQL Server 的恢复。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: raynew
ms.openlocfilehash: 4eaaff859811e4d97cbd4f73231d702285792064
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285439"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>关于 Azure VM 中的 SQL Server 备份

SQL Server 数据库属于关键工作负荷，要求较低的恢复点目标 (RPO) 和长期保留。 可以使用 [Azure 备份](backup-overview.md)来备份 Azure VM 上运行的 SQL Server 数据库。

## <a name="backup-process"></a>备份过程

此解决方案利用 SQL 本机 API 来备份 SQL 数据库。

* 指定要保护的 SQL Server VM 并查询其中的数据库后，Azure 备份服务将在此 VM 上以 `AzureBackupWindowsWorkload`  扩展名安装工作负载备份扩展。
* 此扩展包含协调器和 SQL 插件。 协调器负责触发多种操作（如配置备份、备份和还原）的工作流，插件负责实际数据流。
* 为了发现此 VM 上的数据库，Azure 备份创建帐户  `NT SERVICE\AzureWLBackupPluginSvc`。 此帐户用于备份和还原，需要拥有 SQL sysadmin 权限。 Azure 备份将  `NT AUTHORITY\SYSTEM`  帐户用于数据库发现/查询，因此此帐户需是 SQL 上的公共登录名。 如果 SQL Server VM 不是从 Azure 市场创建的，你可能会收到错误 UserErrorSQLNoSysadminMembership ****。 如果发生此错误，请 [遵照这些说明](backup-azure-sql-database.md)予以解决。
* 在所选数据库上触发配置保护后，备份服务将通过备份计划和其他策略详细信息设置协调器，扩展在 VM 上本地缓存协调器 
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
**支持的地理区域** | 澳大利亚东南部 (ASE)、澳大利亚东部 (AE) <br> 巴西南部 (BRS)<br> 加拿大中部 (CNC)、加拿大东部 (CE)<br> 东南亚 (SEA)、东亚 (EA) <br> 美国东部 (EUS)、美国东部 2 (EUS2)、美国中西部 (WCUS)、美国西部 (WUS)；美国西部 2 (WUS 2) 美国中北部 (NCUS) 美国中部 (CUS) 美国中南部 (SCUS) <br> 印度中部 (INC)、印度南部 (INS) <br> 日本东部 (JPE)、日本西部 (JPW) <br> 韩国中部 (KRC)、韩国南部 (KRS) <br> 北欧 (NE)、西欧 <br> 英国南部 (UKS)、英国西部 (UKW)
**受支持的操作系统** | Windows Server 2016、Windows Server 2012 R2、Windows Server 2012<br/><br/> 目前不支持 Linux。
**支持的 SQL Server 版本** | SQL Server 2017、SQL Server 2016、SQL Server 2014、SQL Server 2012。<br/><br/> Enterprise、Standard、Web、Developer、Express。
**支持的 .NET 版本** | 安装在 VM 上的 .NET Framework 4.5.2 及更高版本

## <a name="feature-consideration-and-limitations"></a>功能注意事项和限制

- SQL Server 备份可配置在 Azure 门户或 PowerShell 中。 我们不支持 CLI。
- 运行 SQL Server 的 VM 需要建立 Internet 连接才能访问 Azure 公共 IP 地址。
- 不支持 SQL Server Always On 故障转移群集实例 (FCI)。
- 不支持对镜像数据库和数据库快照执行备份和还原操作。
- 使用多个备份解决方案来备份独立的 SQL Server 实例或 SQL Always on 可用性组可能导致备份失败；避免执行此操作。
- 如果通过相同或不同的解决方案单独备份可用性组的两个节点，可能也会导致备份失败。 Azure 备份可检测和保护与保管库同一区域内的所有节点。 如果 SQL Server Always on 可用性组跨多个 Azure 区域，请从具有主节点的区域设置备份。 Azure 备份可根据备份首选项检测并保护可用性组中的所有数据库。  
- Azure 备份支持只读数据库的仅完整备份和仅复制完整备份类型
- 无法保护包含大量文件的数据库。 支持的最大文件数约为 1000。  
- 在一个保管库中最多可以备份约 2000 个 SQL Server 数据库。 如果有大量数据库，可创建多个保管库。
- 一次最多可配置 50 个数据库的备份；此限制有助于优化备份负载。
- 我们支持最高 2TB 大小的数据库；对于超过此大小的数据库，可能会出现性能问题。
- 若要了解每个服务器可以保护多少个数据库，我们需要考虑带宽、VM 大小、备份频率、数据库大小等因素。我们正在开发一个规划器，以帮助你自行计算这些数字。 我们很快将发布此规划器。
- 对于可用性组，将基于几个因素从不同节点获取备份。 下面概述了可用性组的备份行为。

### <a name="backup-behavior-in-case-of-always-on-availability-groups"></a>Always on 可用性组的备份行为

根据备份首选项和备份类型（完整/差异/日志/仅复制完整），从特定节点（主要/次要）获取备份。

- **备份首选项：主要节点**

**备份类型** | **Node**
    --- | ---
    完整 | 主要
    差异 | 主要
    日志 |  主要
    仅复制完整 |  主要

- **备份首选项：仅次要节点**

**备份类型** | **Node**
--- | ---
完整 | 主要
差异 | 主要
日志 |  辅助
仅复制完整 |  辅助

- **备份首选项：次要节点**

**备份类型** | **Node**
--- | ---
完整 | 主要
差异 | 主要
日志 |  辅助
仅复制完整 |  辅助

- **无备份首选项**

**备份类型** | **Node**
--- | ---
完整 | 主要
差异 | 主要
日志 |  辅助
仅复制完整 |  辅助

## <a name="fix-sql-sysadmin-permissions"></a>修复 SQL sysadmin 权限

  如果由于出现 **UserErrorSQLNoSysadminMembership** 错误而需要解决权限问题，请执行以下操作：

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


## <a name="next-steps"></a>后续步骤

- [了解有关](backup-sql-server-database-azure-vms.md)备份 SQL Server 数据库的信息。
- [了解](restore-sql-database-azure-vm.md)如何还原已备份的 SQL Server 数据库。
- [了解](manage-monitor-sql-database-backup.md)如何管理已备份的 SQL Server 数据库。

---
title: 将 SQL Server 数据库备份到 Azure | Microsoft Docs
description: 本教程介绍如何将 SQL Server 备份到 Azure， 此外还介绍 SQL Server 的恢复。
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 6/1/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 4ae64fefb58840214104a4e1cb338ec404fac1a8
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235407"
---
# <a name="back-up-sql-server-database-in-azure"></a>在 Azure 中备份 SQL 数据库

SQL Server 数据库属于关键工作负荷，要求较低的恢复点目标 (RPO) 和长期保留。 Azure 备份提供无需基础结构的 SQL Server 备份解决方案，这意味着，不需要管理复杂的备份服务器、管理代理或备份存储。 Azure 备份提供跨所有 SQL 服务器甚至不同工作负荷的集中式备份管理。

 本文内容：

> [!div class="checklist"]
> * 将 SQL Server 备份到 Azure 所要满足的先决条件
> * 创建和使用恢复服务保管库
> * 配置 SQL Server 数据库备份
> * 为恢复点设置备份（或保留）策略
> * 如何还原数据库

在开始执行本文中的过程之前，应在 Azure 中运行一个 SQL Server。 [可以使用 SQL 市场虚拟机快速创建 SQL Server](../sql-database/sql-database-get-started-portal.md)。

## <a name="public-preview-limitations"></a>公共预览版的限制

公共预览版存在以下已知限制。

- SQL 虚拟机需要建立 Internet 连接才能访问 Azure 公共 IP 地址。 有关详细信息，请参阅[建立网络连接](backup-azure-sql-database.md#establish-network-connectivity)部分。
- 在一个恢复服务保管库中最多可以保护 2000 个 SQL 数据库。 应将其他 SQL 数据库存储在单独的恢复服务保管库中。
- [分布式可用性组备份存在限制](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017)。
- 不支持 SQL 故障转移群集实例 (FCI)。
- 使用 Azure 门户配置 Azure 备份，以保护 SQL Server 数据库。 目前不支持 Azure PowerShell、CLI 和 REST API。

## <a name="supported-azure-geos"></a>支持的 Azure 地理区域

- 澳大利亚东南部 (ASE) 
- 巴西南部 (BRS)
- 加拿大中部 (CNC)
- 加拿大东部 (CE)
- 美国中部 (CUS)
- 东亚 (EA)
- 东澳大利亚 (AE) 
- 美国东部 (EUS)
- 美国东部 2 (EUS2)
- 日本东部 (JPE)
- 日本西部 (JPW)
- 印度中部 (INC) 
- 印度南部 (INS)
- 韩国中部 (KRC)
- 韩国南部 (KRS)
- 美国中北部 (NCUS) 
- 北欧 (NE) 
- 美国中南部 (SCUS) 
- 东南亚 (SEA)
- 英国南部 (UKS) 
- 英国西部 (UKW) 
- 西欧 (WE) 
- 美国西部 (WUS)
- 美国中西部 (WCUS)
- 美国西部 2 (WUS 2) 

## <a name="supported-operating-systems-and-versions-of-sql-server"></a>支持的 SQL server 操作系统和版本

以下受支持的 SQL Server 操作系统和版本适用于 SQL 市场 Azure 虚拟机，而不适用于非市场虚拟机（其中的 SQL Server 是手动安装的）。

### <a name="supported-operating-systems"></a>支持的操作系统

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

目前不支持 Linux。

### <a name="supported-versionseditions-of-sql-server"></a>支持的 SQL Server 版本

- SQL 2012 Enterprise、Standard、Web、Developer、Express
- SQL 2014 Enterprise、Standard、Web、Developer、Express
- SQL 2016 Enterprise、Standard、Web、Developer、Express
- SQL 2017 Enterprise、Standard、Web、Developer、Express


## <a name="prerequisites-for-using-azure-backup-to-protect-sql-server"></a>使用 Azure 备份保护 SQL Server 所要满足的先决条件 

在备份 SQL Server 数据库之前，请检查以下条件。 :

- 在托管 SQL Server 的虚拟机所在的同一区域或位置标识或[创建恢复服务保管库](backup-azure-sql-database.md#create-a-recovery-services-vault)。
- 检查备份 SQL 数据库所需的[虚拟机权限](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms)。
- [SQL 虚拟机已建立网络连接](backup-azure-sql-database.md#establish-network-connectivity)。

> [!NOTE]
> 每次只能使用一个备份解决方案来备份 SQL Server 数据库。 在使用此功能之前，请禁用其他任何 SQL 备份，否则备份会受到干扰并失败。 可以结合 SQL 备份一起为 IaaS VM 启用 Azure 备份，这不会产生冲突 
>

如果环境满足这些条件，请转到[配置保管库以保护 SQL 数据库](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database)部分。 如果不满足上述任何先决条件，请继续阅读本部分。


## <a name="establish-network-connectivity"></a>建立网络连接

对于所有操作，SQL 虚拟机需要与 Azure 公共 IP 地址建立连接。 在未连接到公共 IP 地址的情况下，SQL 虚拟机操作（例如数据库发现、配置备份、计划的备份、还原恢复点等）将会失败。 使用以下选项之一提供备份流量的明确路径：

- 将 Azure 数据中心 IP 范围加入白名单 - 若要将 Azure 数据中心 IP 范围加入白名单，请使用[下载中心页了解有关 IP 范围的详细信息和说明](https://www.microsoft.com/download/details.aspx?id=41653)。 
- 部署 HTTP 代理服务器用于路由流量 - 在 VM 中备份 SQL 数据库时，VM 上的备份扩展将使用 HTTPS API 将管理命令发送到 Azure 备份，并将数据发送到 Azure 存储。 备份扩展还使用 Azure Active Directory (AAD) 进行身份验证。 通过 HTTP 代理路由这三个服务的备份扩展流量，因为它是为了访问公共 Internet 而配置的唯一组件。

这些选项之间的利弊体现在易管理性、精细控制和成本方面。

> [!NOTE]
>正式版会提供 Azure 备份的服务标记。
>

| 选项 | 优点 | 缺点 |
| ------ | ---------- | ------------- |
| 将 IP 范围加入允许列表 | 无额外成本。 <br/> 若要在 NSG 中打开访问权限，请使用 **Set-AzureNetworkSecurityRule** cmdlet。 | 管理起来很复杂，因为受影响的 IP 范围会不断变化。 <br/>允许访问整个 Azure，而不只是存储。|
| 使用 HTTP 代理   | 允许在代理中对存储 URL 进行精细控制。 <br/>在单个位置通过 Internet 访问 VM。 <br/> 不受 Azure IP 地址变化的影响。 | 通过代理软件运行 VM 带来的额外成本。 |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>设置非市场 SQL VM 的权限

若要备份虚拟机，Azure 备份要求安装 **AzureBackupWindowsWorkload** 扩展。 如果使用的是 Azure 市场虚拟机，请转到[发现 SQL Server 数据库](backup-azure-sql-database.md#discover-sql-server-databases)。 如果托管 SQL 数据库的虚拟机不是从 Azure 市场创建的，请完成以下部分来安装扩展并设置相应的权限。 除了 **AzureBackupWindowsWorkload** 扩展以外，Azure 备份还需要 SQL sysadmin 特权才能保护 SQL 数据库。 发现虚拟机上的数据库时，Azure 备份会创建帐户 NT Service\AzureWLBackupPluginSvc。 要使 Azure 备份能够发现 SQL 数据库，NT Service\AzureWLBackupPluginSvc 帐户必须具有 SQL 登录和 SQL sysadmin 权限。 以下过程说明如何提供这些权限。

配置权限：

1. 在 [Azure 门户](https://portal.azure.com)中，打开用来保护 SQL 数据库的恢复服务保管库。
2. 在保管库仪表板菜单中，单击“+备份”打开“备份目标”菜单。

   ![单击“+备份”打开“备份目标”菜单](./media/backup-azure-sql-database/open-backup-menu.png)

3. 在“备份目标”菜单的“工作负荷的运行位置”菜单中，保留默认值“Azure”。

4. 在“要备份哪些内容”菜单中展开下拉菜单，然后选择“Azure VM 中的 SQL Server”。

    ![单击“+备份”打开“备份目标”菜单](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    “备份目标”菜单显示两个新步骤：“发现 VM 中的数据库”和“配置备份”。 “发现 VM 中的数据库”开始搜索 Azure 虚拟机。

    ![显示新的备份目标步骤](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. 单击“开始发现”以搜索订阅中不受保护的虚拟机。 根据订阅中不受保护的虚拟机数量，可能需要一段时间才能搜索完所有虚拟机。

    ![等待中的备份](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    发现不受保护的虚拟机后，它会显示在列表中。 不受保护的虚拟机按其虚拟机名称和资源组列出。 可能有多个虚拟机同名。 但是，同名的虚拟机属于不同的资源组。 如果预期的虚拟机未显示在列表中，请检查该虚拟机是否已在保管库中受保护。

6. 在虚拟机列表中，选择包含要备份的 SQL 数据库的 VM，然后单击“发现数据库”。 

    发现过程会在该虚拟机上安装 **AzureBackupWindowsWorkload** 扩展。 该扩展可让 Azure 备份服务与虚拟机通信，以便能够备份 SQL 数据库。 安装扩展后，Azure 备份会在虚拟机上创建 Windows 虚拟服务帐户 **NT Service\AzureWLBackupPluginSvc**。 该虚拟服务帐户需要 SQL sysadmin 权限。 在安装虚拟服务帐户过程中，如果出现错误 **UserErrorSQLNoSysadminMembership**，请参阅[修复 SQL sysadmin 权限](backup-azure-sql-database.md#fixing-sql-sysadmin-permissions)部分。

    通知区域会显示数据库发现的进度。 根据虚拟机上的数据库数量，可能需要一段时间才能完成该作业。 发现选定的数据库后，会显示一条成功消息。

    ![成功部署通知消息](./media/backup-azure-sql-database/notifications-db-discovered.png)

将数据库与恢复服务保管库关联后，下一步是[配置备份](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database)。

### <a name="fixing-sql-sysadmin-permissions"></a>修复 SQL sysadmin 权限

在安装过程中，如果出现错误 **UserErrorSQLNoSysadminMembership**，请使用拥有 SQL sysadmin 权限的帐户登录到 SQL Server Management Studio (SSMS)。 除非需要特殊权限，否则应该能够使用 Windows 身份验证来识别帐户。

1. 在 SQL 服务器上，打开“安全/登录名”文件夹。

    ![打开 SQL Server 和安全与登录名文件夹来查看帐户](./media/backup-azure-sql-database/security-login-list.png)

2. 在“登录名”文件夹中，右键单击并选择“新建登录名”，然后在“登录名 - 新建”对话框中单击“搜索”

    ![在“登录名 - 新建”对话框中打开“搜索”](./media/backup-azure-sql-database/new-login-search.png)

3. 由于在虚拟机注册和 SQL 发现阶段已创建 Windows 虚拟服务帐户 **NT Service\AzureWLBackupPluginSvc**，因此请输入“输入要选择的对象名称”对话框中显示的帐户名。 单击“检查名称”以解析名称。 

    ![单击“检查名称”按钮解析未知的服务名称](./media/backup-azure-sql-database/check-name.png)

4. 单击“确定”关闭“选择用户或组”对话框。

5. 在“服务器角色”对话框中，请确保“sysadmin”角色已选中。 然后单击“确定”关闭“登录名 - 新建”。

    ![确保 sysadmin 服务器角色已选中](./media/backup-azure-sql-database/sysadmin-server-role.png)

    现在，所需的权限应会存在。

6. 尽管修复了权限错误，但仍需将数据库与恢复服务保管库相关联。 在 Azure 门户的“受保护的服务器”列表中，右键单击有错误的服务器，然后选择“重新发现数据库”。

    ![验证服务器是否拥有相应的权限](./media/backup-azure-sql-database/check-erroneous-server.png)

    通知区域会显示数据库发现的进度。 根据虚拟机上的数据库数量，可能需要一段时间才能完成该作业。 找到选定的数据库后，通知区域会显示一条成功消息。

    ![成功部署通知消息](./media/backup-azure-sql-database/notifications-db-discovered.png)

将数据库与恢复服务保管库关联后，下一步是[配置备份](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database)。

[!INCLUDE [Section explaining how to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>发现 SQL Server 数据库

Azure 备份可以发现 SQL Server 实例上的所有数据库，因此你可以根据备份要求对其进行保护。 使用以下过程识别托管 SQL 数据库的虚拟机。 识别虚拟机后，Azure 备份会安装一个轻量的扩展来发现 SQL Server 数据库。

1. 在 [Azure 门户](https://portal.azure.com/)中登录到自己的订阅。
2. 在左侧菜单中，选择“所有服务”。

    ![在主菜单中选择“所有服务”选项](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. 在“所有服务”对话框中，键入“恢复服务”。 开始键入时，会根据输入筛选资源列表。 出现“恢复服务保管库”时，请选择它。

    ![在“所有服务”对话框中键入“恢复服务”](./media/backup-azure-sql-database/all-services.png) <br/>

    此时会显示订阅中的恢复服务保管库列表。 

4. 在恢复服务保管库列表中，选择要用来保护 SQL 数据库的保管库。

5. 在保管库仪表板菜单中，单击“+备份”打开“备份目标”菜单。

   ![单击“+备份”打开“备份目标”菜单](./media/backup-azure-sql-database/open-backup-menu.png)

6. 在“备份目标”菜单的“工作负荷的运行位置”菜单中，保留默认值“Azure”。

7. 在“要备份哪些内容”菜单中展开下拉菜单，然后选择“Azure VM 中的 SQL Server”。

    ![单击“+备份”打开“备份目标”菜单](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    选择后，“备份目标”菜单会显示两个步骤：“发现 VM 中的数据库”和“配置备份”。 

    ![显示新的备份目标步骤](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. 单击“开始发现”以搜索订阅中不受保护的虚拟机。 根据订阅中不受保护的虚拟机数量，可能需要一段时间才能搜索完所有虚拟机。

    ![等待中的备份](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    发现不受保护的虚拟机后，它会显示在列表中。 可能有多个虚拟机同名。 但是，同名的多个虚拟机属于不同的资源组。 不受保护的虚拟机按其虚拟机名称和资源组列出。 如果预期的虚拟机未列出，请检查该虚拟机是否已在保管库中受保护。

9. 在虚拟机列表中，选择包含要保护的 SQL 数据库的虚拟机对应的复选框，然后单击“发现数据库”。

    Azure 备份会发现虚拟机上的所有 SQL 数据库。 有关在数据库发现阶段发生的情况的信息，请参阅以下部分：[发现 SQL 数据库时的后端操作](backup-azure-sql-database.md#backend-operations-when-discovering-sql-databases)。 发现 SQL 数据库之后，便可以[配置备份作业](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database)。

### <a name="backend-operations-when-discovering-sql-databases"></a>发现 SQL 数据库时的后端操作

使用“发现数据库”工具时，Azure 备份会在后台执行以下操作：

- 将虚拟机注册到恢复服务保管库，以进行工作负荷备份。 注册的虚拟机上的所有数据库只能备份到此恢复服务保管库。 

- 在虚拟机上安装 **AzureBackupWindowsWorkload** 扩展。 备份 SQL 数据库是一种无代理的解决方案，也就是说，在虚拟机上安装扩展后，无需在 SQL 数据库上安装任何代理。

- 在虚拟机上创建服务帐户 **NT Service\AzureWLBackupPluginSvc**。 所有备份和还原操作使用该服务帐户。 **NT Server\AzureWLBackupPluginSvc** 需要 SQL sysadmin 权限。 所有 SQL 市场虚拟机上都已安装 SqlIaaSExtension，AzureBackupWindowsWorkload 使用 SQLIaaSExtension 自动获取所需的权限。 如果虚拟机上未安装 SqlIaaSExtension，则“发现数据库”操作将会失败，并且会出现错误消息 **UserErrorSQLNoSysAdminMembership**。 若要添加用于备份的 sysadmin 权限，请遵照[为非市场 SQL VM 设置 Azure 备份权限](backup-azure-sql-database.md#set-permissions-for-non--marketplace-sql-vms)中的说明。

    ![选择 VM 和数据库](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-database"></a>配置 SQL Server 数据库的备份

Azure 备份提供管理服务用于保护 SQL Server 数据库和管理备份作业。 管理和监视功能取决于恢复服务保管库。 

> [!NOTE]
> 每次只能使用一个备份解决方案来备份 SQL Server 数据库。 在使用此功能之前，请禁用其他任何 SQL 备份，否则备份会受到干扰并失败。 可以结合 SQL 备份一起为 IaaS VM 启用 Azure 备份，这不会产生冲突 
>

配置 SQL 数据库的保护：

1. 打开已注册到 SQL 虚拟机的恢复服务保管库。

2. 在保管库仪表板菜单中，单击“+备份”打开“备份目标”菜单。

    ![单击“+备份”打开“备份目标”菜单](./media/backup-azure-sql-database/open-backup-menu.png)

3. 在“备份目标”菜单的“工作负荷的运行位置”菜单中，保留默认值“Azure”。

4. 在“要备份哪些内容”菜单中展开下拉菜单，然后选择“Azure VM 中的 SQL Server”。

    ![单击“+备份”打开“备份目标”菜单](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    选择后，“备份目标”菜单会显示两个步骤：“发现 VM 中的数据库”和“配置备份”。 如果按顺序阅读了本文，则已发现未受保护的虚拟机，并且已将此保管库注册到虚拟机。 现在，可以配置 SQL 数据库的保护。

5. 在“备份目标”菜单中，单击“配置备份”。

    ![显示新的备份目标步骤](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Azure 备份服务会显示包含独立数据库的所有 SQL 实例，以及 SQL AlwaysOn 可用性组。 若要查看 SQL 实例中的独立数据库，请单击实例名称旁边的 V 形图标以查看数据库。 下图显示独立实例和 Always On 可用性组的示例。

    > [!NOTE]
    > 完整备份和差异备份从主节点发生，因为 SQL 平台存在限制。 日志备份可根据备份首选项发生。 由于这种限制，必须注册主节点。
    >

    ![SQL 实例中的数据库列表](./media/backup-azure-sql-database/discovered-databases.png)

    单击 AlwaysOn 可用性组旁边的 V 形图标查看数据库列表。

    ![AlwaysOn 可用性组中的数据库列表](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. 在数据库列表中，选择要保护的所有数据库，然后单击“确定”。

    ![选择要保护的多个数据库](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    一次最多可以选择 50 个数据库。 如果想要保护 50 个以上的数据库，请分多个轮次操作。 保护前 50 个数据库之后，重复此步骤保护下一组数据库。
    > [!Note] 
    > 为了优化备份负载，Azure 备份会将大型备份作业划分为多个批。 一个备份作业中的最大数据库数为 50。
    >
    >

7. 若要创建或选择备份策略，请在“备份”菜单中选择“备份策略”打开菜单。

    ![选择备份策略选项](./media/backup-azure-sql-database/select-backup-policy.png)

8. 在“选择备份策略”下拉菜单中选择备份策略，然后单击“确定”。 有关创建自己的备份策略的信息，请参阅[定义备份策略](backup-azure-sql-database.md#define-a-backup-policy)部分。

    ![从下拉菜单中选择备份策略](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    在“备份策略”菜单的“选择备份策略”下拉菜单中，可以选择： 
    - 默认的 HourlyLogBackup 策略； 
    - 前面为 SQL 创建的现有备份策略；
    - 根据恢复点目标 (RPO) 和保留范围[定义新策略](backup-azure-sql-database.md#define-a-backup-policy)。 

    > [!Note]
    > Azure 备份支持使用基于祖父-父-子备份方案的长期保留策略来优化后端存储消耗，同时满足法规遵从需求。
    >

9. 选择备份策略后，在“备份菜单”中单击“启用备份”。

    ![启用选定的备份策略](./media/backup-azure-sql-database/enable-backup-button.png)

    可在门户的“通知”区域跟踪配置进度。

    ![查看通知区域](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>定义备份策略

备份策略定义由备份创建时间和这些备份的保留时间长度构成的矩阵。 可以使用 Azure 备份来计划三种类型的 SQL 数据库备份：

* 完整备份 - 完整数据库备份将备份整个数据库。 完整备份包含特定数据库或者文件组或文件集中的所有数据，以及用于恢复数据的足够日志。 每天最多可以触发一次完整备份。 可以选择每天或每周创建完整备份。 
* 差异备份 - 差异备份基于最新的、以前的完整数据备份。 差异备份只捕获自完整备份以来发生更改的数据。 每天最多可以触发一次差异备份。 不能在同一天配置完整备份和差异备份。
* 事务日志备份 - 日志备份可以实现时间点还原，精度可达到特定的秒。 最多可以配置为每隔 15 分钟创建事务日志备份。

策略在恢复服务保管库级别创建。 如果有多个保管库，则保管库可以使用相同的备份策略，但必须向每个保管库应用该备份策略。 创建备份策略时，每日完整备份是默认设置。 可以添加差异备份，但仅在将完整备份切换为每周发生时，才能这样做。 以下过程说明如何为 Azure 虚拟机中的 SQL Server 创建备份策略。

创建备份策略

1. 在“备份策略”菜单的“选择备份策略”下拉菜单中，选择“新建”。

   ![创建新的备份策略](./media/backup-azure-sql-database/create-new-backup-policy.png)

    “备份策略”菜单改为提供任何新 SQL Server 备份策略所需的字段。

   ![新备份策略字段](./media/backup-azure-sql-database/blank-new-policy.png)

2. 在“策略名称”中提供一个名称。 

3. 完整备份是必需的。 可以接受完整备份的默认值，或单击“完整备份”编辑策略。

    ![新备份策略字段](./media/backup-azure-sql-database/full-backup-policy.png)

    在完整备份策略中，可以选择“每日”或“每周”作为频率。 如果选择“每日”，请选择备份作业开始时的小时和时区。 如果选择“每日完整备份”，则无法创建差异备份。

   ![每日间隔设置](./media/backup-azure-sql-database/daily-interval.png)

    如果选择“每周”，请选择备份作业开始时的星期、小时和时区。

   ![每周间隔设置](./media/backup-azure-sql-database/weekly-interval.png)

4. 默认情况下，所有“保留范围”选项（每日、每周、每月和每年）已选中。 请取消选中任何不想要使用的保留范围限制，并设置要使用的间隔。 在“完整备份策略”菜单中，单击“确定”接受设置。

   ![保留范围间隔设置](./media/backup-azure-sql-database/retention-range-interval.png)

    恢复点已根据其保留范围标记为保留。 例如，如果选择每日完整备份，则每天只触发一次完整备份。 根据每周保留策略，将会根据每周保持范围来标记和保留特定日期的备份。 每月和每年保留范围的行为类似。

5. 若要添加差异备份策略，请单击“差异备份”打开其菜单。 

   ![打开差异策略](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    在“差异备份策略”菜单中，选择“启用”打开频率和保留控件。 每天最多可以触发一次差异备份。
    > [!Important] 
    > 差异备份最多可以保留 180 天。 如果需要保留更长时间，必须使用完整备份，而不能使用差异备份。
    >

   ![编辑差异策略](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    单击“确定”保存策略，并返回备份策略主菜单。

6. 若要添加事务日志备份策略，请单击“日志备份”打开其菜单。 在“日志备份”菜单中选择“启用”，并设置频率和保留控件。 日志备份最多可以每隔 15 分钟发生一次，最多可以保留 35 天。 单击“确定”保存策略，并返回备份策略主菜单。

   ![编辑日志备份策略](./media/backup-azure-sql-database/log-backup-policy-editor.png)

7. 选择是否要启用 SQL 备份压缩。 默认已禁用压缩。

    Azure 备份在后端使用 SQL 本机备份压缩。

8. 完成备份策略的所有编辑后，单击“确定”。 

   ![差异备份保留范围](./media/backup-azure-sql-database/differential-backup-policy.png)

## <a name="restore-a-sql-database"></a>还原 SQL 数据库

Azure 备份提供使用事务日志备份将单个数据库还原到特定的日期或时间的功能，精度可达到特定的秒。 根据提供的还原时间，Azure 备份会自动确定还原数据所需的相应完整、差异和日志链备份。

或者，可以选择特定的完整备份或差异备份来还原到特定的恢复点而不是特定的时间。

还原数据库

1. 打开已注册到 SQL 虚拟机的恢复服务保管库。

2. 在保管库仪表板中，选择“使用情况”备份项打开“备份项”菜单。

    ![单击“+备份”打开“备份目标”菜单](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png)。

3. 在“备份项”菜单中，选择备份管理类型“Azure VM 中的 SQL”。 

    ![单击“+备份”打开“备份目标”菜单](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    “备份项”列表会进行调整，以显示 SQL 数据库列表。 

4. 在 SQL 数据库列表中，选择要还原的数据库。

    ![从列表中选择“Azure VM 中 SQL”](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    选择数据库时，会打开其菜单。 此菜单提供数据库的备份详细信息，包括：

    * 最旧和最新的还原点；
    * 过去 24 小时的日志备份状态（适用于完整和批量日志记录恢复模型中的数据库，如果对事务日志备份进行了相应配置）

5. 在选定的数据库菜单中，单击“还原数据库”打开“还原”菜单。

    ![选择“还原数据库”](./media/backup-azure-sql-database/restore-db-button.png)

    此时会打开“还原”菜单，以及“还原配置”菜单。 “还原配置”菜单是配置还原的第一步。 在此菜单中，选择要将数据还原到哪个位置。 选项包括：
    * 备用位置 - 如果想要将数据库还原到备用位置，同时仍保留原始源数据库，请使用此选项。
    * 覆盖数据库 - 将数据还原到原始源所在的同一 SQL Server 实例。 此选项会覆盖原始数据库。

    > [!Important]
    > 如果选定的数据库属于 Always On 可用性组，则 SQL 不允许覆盖数据库。 在此情况下，只会启用“备用位置”选项。
    >

    ![单击“配置”打开“还原配置”菜单](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>还原到备用位置

此过程会逐步将数据还原到备用位置。 如果希望在还原时覆盖数据库，请转到[还原并覆盖数据库](backup-azure-sql-database.md#restore-and-overwrite-the-database)部分。 此过程假设已打开恢复服务保管库，并且在“还原配置”菜单中操作。 否则，请从[还原 SQL 数据库](backup-azure-sql-database.md#restore-a-sql-database)着手。

“服务器”下拉菜单仅显示已注册到恢复服务保管库的 SQL 服务器。 如果所需的服务器不在“服务器”列表中，请参阅[发现 SQL Server 数据库](backup-azure-sql-database.md#discover-sql-server-databases)部分来查找服务器。 在数据库发现过程中，所有新服务器会注册到恢复服务保管库。

1. 在“还原配置”菜单中：

    * 选择“备用位置”。
    * 对于“服务器”，选择要在其中还原数据库的 SQL 服务器。
    * 在“实例”下拉菜单中选择一个 SQL 实例。
    * 在“还原数据库名称”对话框中，提供目标数据库的名称。
    * 如果适用，请选择“当选定的 SQL 实例上已存在同名的数据库时覆盖”。
    * 单击“确定”完成目标配置，并继续选择还原点。

    ![在“还原配置”菜单中选择备用位置、实例和名称](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. 在“选择还原点”菜单中，可以选择“日志(时间点)”或“完整和差异”还原点。 如果想要还原到特定的时间点日志，请继续执行此步骤。 如果想要还原完整或差异还原点，请转到步骤 3。

    ![选择还原点菜单](./media/backup-azure-sql-database/recovery-point-menu.png)

    时间点还原仅适用于采用完整和批量日志记录恢复模型的数据库日志备份。 还原到特定的时间点：

    1. 选择“日志(时间点)”作为还原选项。

        ![选择还原点类型](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. 在“还原日期/时间”下，单击日历图标打开日历。 以粗体显示的日期包含恢复点，当前日期会突出显示。 在日历中选择一个包含恢复点的日期。 不能选择不包含恢复点的日期。

        ![打开日历](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        选择日期后，时间线图会显示连续范围内的可用恢复点。

    3. 使用时间线图或“时间”对话框，指定恢复点的特定时间，然后单击“确定”完成“还原点”步骤。
    
       ![打开日历](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        “选择还原点”菜单关闭，“高级配置”菜单打开。

       ![高级配置菜单](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. 在“高级配置”菜单中：

        * 要使数据库在还原后保持不可运行状态，请在“使用 NORECOVERY 还原”菜单中选择“已启用”。
        * 若要更改目标服务器上的还原位置，请在“目标”列中提供新路径。
        * 单击“确定”以确认设置，并关闭“高级配置”。

    5. 在“还原”菜单中，单击“还原”启动还原作业。 在“通知”区域中，可以跟踪进度。 还可以在数据库还原作业中跟踪进度。

       ![高级配置菜单](./media/backup-azure-sql-database/restore-job-notification.png)

3. 在“选择还原点”菜单中，选择一个恢复点。 可以选择“日志(时间点)”或“完整和差异”。 如果想要还原时间点日志，请返回步骤 2。 此步骤可还原特定的完整或差异还原点。 使用此选项时，可以看到过去 30 天的所有完整和和差异恢复点。 若要查看 30 天以前的恢复点，请单击“筛选”打开“筛选还原点”菜单。 如果选择差异恢复点，Azure 备份首先会还原相应的完整恢复点，然后应用选定的差异恢复点。

    ![选择还原点菜单](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. 在“选择还原点”菜单中，选择“完整和差异”。

       ![选择还原点菜单](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        此时会显示可用恢复点的列表。

    2. 在恢复点列表中选择一个恢复点，然后单击“确定”完成“还原点”过程。 

        ![选择完整恢复点](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        “还原点”菜单关闭，“高级配置”菜单打开。

        ![高级配置菜单](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. 在“高级配置”菜单中：

        * 要使数据库在还原后保持不可运行状态，请在“使用 NORECOVERY 还原”菜单中选择“已启用”。 “使用 NORECOVERY 还原”默认已禁用。
        * 若要更改目标服务器上的还原位置，请在“目标”列中提供新路径。
        * 单击“确定”以确认设置，并关闭“高级配置”。

    4. 在“还原”菜单中，单击“还原”启动还原作业。 在“通知”区域中，可以跟踪进度。 还可以在数据库还原作业中跟踪进度。

       ![高级配置菜单](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>还原并覆盖数据库

此过程逐步还原数据并覆盖数据库。 若要还原到备用位置，请转到[还原到备用位置](backup-azure-sql-database.md#restore-to-an-alternate-location)部分。 此过程假设已打开恢复服务保管库，并在“还原配置”菜单中操作（参阅下图）。 否则，请从[还原 SQL 数据库](backup-azure-sql-database.md#restore-a-sql-database)着手。

![单击“配置”打开“还原配置”菜单](./media/backup-azure-sql-database/restore-overwrite-db.png)

“服务器”下拉菜单仅显示已注册到恢复服务保管库的 SQL 服务器。 如果所需的服务器不在“服务器”列表中，请参阅[发现 SQL Server 数据库](backup-azure-sql-database.md#discover-sql-server-databases)部分来查找服务器。 在数据库发现过程中，所有新服务器会注册到恢复服务保管库。

1. 在“还原配置”菜单中选择“覆盖数据库”，并单击“确定”完成目标配置。 

   ![单击“覆盖数据库”](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    忽略“服务器”、“实例”和“还原数据库名称”对话框。

2. 在“选择还原点”菜单中，可以选择“日志(时间点)”或“完整和差异”还原点。 若要还原时间点日志，请继续执行此步骤。 若要还原完整和差异还原点，请转到步骤 3。

    ![选择还原点菜单](./media/backup-azure-sql-database/recovery-point-menu.png)

    时间点还原仅适用于采用完整和批量日志记录恢复模型的数据库日志备份。 选择要还原到的时间点中的特定秒：

    1. 选择“日志(时间点)”作为还原选项。

        ![选择还原点类型](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. 在“还原日期/时间”下，单击日历图标打开日历。 以粗体显示的日期包含恢复点，当前日期会突出显示。 在日历中选择一个包含恢复点的日期。 不能选择不包含恢复点的日期。

        ![打开日历](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        选择日期后，时间线图会显示可用恢复点。

    3. 使用时间线图或“时间”对话框，指定恢复点的特定时间，然后单击“确定”完成“还原点”步骤。
    
       ![打开日历](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        “选择还原点”菜单关闭，“高级配置”菜单打开。

       ![高级配置菜单](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. 在“高级配置”菜单中：

        * 要使数据库在还原后保持不可运行状态，请在“使用 NORECOVERY 还原”菜单中选择“已启用”。
        * 若要更改目标服务器上的还原位置，请在“目标”列中提供新路径。
        * 单击“确定”以确认设置，并关闭“高级配置”。

    5. 在“还原”菜单中，单击“还原”启动还原作业。 在“通知”区域中，可以跟踪进度。 还可以在数据库还原作业中跟踪进度。

       ![高级配置菜单](./media/backup-azure-sql-database/restore-job-notification.png)

3. 在“选择还原点”菜单中，选择一个恢复点。 可以选择“日志(时间点)”或“完整和差异”。 如果想要还原时间点日志，请返回步骤 2。 此步骤可还原特定的完整或差异还原点。 使用此选项时，可以看到过去 30 天的所有完整和和差异恢复点。 若要查看 30 天以前的恢复点，请单击“筛选”打开“筛选还原点”菜单。 如果选择差异恢复点，Azure 备份首先会还原相应的完整恢复点，然后应用选定的差异恢复点。

    ![选择还原点菜单](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. 在“选择还原点”菜单中，选择“完整和差异”。

       ![选择还原点菜单](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        此时会显示可用恢复点的列表。

    2. 在恢复点列表中选择一个恢复点，然后单击“确定”完成“还原点”过程。 

        ![选择完整恢复点](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        “还原点”菜单关闭，“高级配置”菜单打开。

        ![高级配置菜单](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. 在“高级配置”菜单中：

        * 要使数据库在还原后保持不可运行状态，请在“使用 NORECOVERY 还原”菜单中选择“已启用”。 “使用 NORECOVERY 还原”默认已禁用。
        * 若要更改目标服务器上的还原位置，请在“目标”列中提供新路径。
        * 单击“确定”以确认设置，并关闭“高级配置”。

    4. 在“还原”菜单中，单击“还原”启动还原作业。 在“通知”区域中，可以跟踪进度。 还可以在数据库还原作业中跟踪进度。

       ![高级配置菜单](./media/backup-azure-sql-database/restore-job-notification.png)


## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>管理 Azure VM 上 SQL 的 Azure 备份操作

本部分提供有关适用于 Azure 虚拟机上 SQL 的各种 Azure 备份管理操作的信息。 存在以下高级操作：

* 监视作业
* 备份警报
* 停止 SQL 数据库的保护
* 恢复 SQL 数据库的保护
* 触发临时备份作业
* 取消注册 SQL Server

### <a name="monitor-jobs"></a>监视作业

Azure 备份使用 SQL 本机 API 执行所有备份操作。 使用本机 API 可以从 msdb 数据库中的 [SQL backupset 表](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017)提取所有作业信息。 此外，Azure 备份在备份作业门户中显示所有手动触发的作业或临时作业。 门户中显示的作业包括：所有配置备份操作、还原操作、注册和发现数据库操作，以及停止备份操作。 还可以使用 OMS Log Analytics 监视所有计划的作业。 使用 Log Analytics 可以消除作业混杂情况，并灵活细致地监视或筛选特定的作业。

![高级配置菜单](./media/backup-azure-sql-database/jobs-list.png)

### <a name="backup-alerts"></a>备份警报

由于日志备份每隔 15 分钟就会发生一次，因此，监视备份作业有时会很繁琐。 Azure 备份提供按任何备份失败触发的电子邮件警报，针对这种潜在繁琐的情况做了规划。 警报按错误代码在数据库级别合并。 例如，如果某个数据库多次发生备份失败，则你不会收到每次失败的警报，而会收到第一次失败的电子邮件。 然后，可以登录 Azure 门户来监视该数据库的后续失败。 

监视备份警报：

1. 在 [Azure 门户](https://portal.azure.com)中登录到自己的 Azure 订阅。

2. 打开已注册到 SQL 虚拟机的恢复服务保管库。

3. 在“恢复服务保管库”菜单中，选择“警报和事件”。 

   ![高级配置菜单](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. 在“警报和事件”菜单中，选择“备份警报”查看警报列表。

   ![高级配置菜单](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-on-a-sql-server-database"></a>停止 SQL Server 数据库的保护

如果停止保护 SQL Server 数据库，则 Azure 备份会要求你确认是否保留恢复点。 可通过两种方法来停止保护 SQL 数据库：

* 停止所有将来的备份作业并删除所有恢复点；
* 停止所有将来的备份作业，但保留恢复点 

保留恢复点会产生成本，因为 SQL 的恢复点附带 SQL 保护实例的费用，加上存储使用费。 有关 SQL 的 Azure 备份定价信息，请参阅 [Azure 备份定价页](https://azure.microsoft.com/pricing/details/backup/)。 停止数据库的保护：

1. 打开已注册到 SQL 虚拟机的恢复服务保管库。

2. 在保管库仪表板中，选择“使用情况”备份项打开“备份项”菜单。

    ![单击“+备份”打开“备份目标”菜单](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png)。

3. 在“备份项”菜单中，选择备份管理类型“Azure VM 中的 SQL”。 

    ![单击“+备份”打开“备份目标”菜单](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    “备份项”列表会进行调整，以显示 SQL 数据库列表。 

4. 在 SQL 数据库列表中，选择要停止保护的数据库。

    ![从列表中选择“Azure VM 中 SQL”](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    选择数据库时，会打开其菜单。 

5. 在选定的数据库菜单中，单击“停止备份”以停止保护该数据库。

    ![选择“还原数据库”](./media/backup-azure-sql-database/stop-db-button.png)

    此时会打开“停止备份”菜单。

6. 在“停止备份”菜单中，选择“保留备份数据”或“删除备份数据”。 （可选）可以提供停止保护的原因和备注。

    ![选择“还原数据库”](./media/backup-azure-sql-database/stop-backup-button.png)

7. 单击“停止备份”以停止数据库的保护。 

### <a name="resume-protection-for-a-sql-database"></a>恢复 SQL 数据库的保护

如果在停止 SQL 数据库的保护时选择了“保留备份数据”选项，则可以恢复保护。 如果未保留备份数据，则无法恢复保护。 

1. 若要恢复 SQL 数据库的保护，请打开备份项并单击“恢复备份”。

    ![恢复数据库保护](./media/backup-azure-sql-database/resume-backup-button.png)

   此时会打开“备份策略”菜单。

2. 在“备份策略”菜单中选择策略，并单击“保存”。

### <a name="trigger-an-adhoc-backup"></a>触发临时备份

如果需要，可以触发临时备份。 有四种类型的临时备份。 有关每种类型的详细信息，请参阅 [SQL 备份类型](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups)一文。

* 完整备份
* 仅复制完整备份
* 差异备份
* 日志备份

### <a name="unregister-a-sql-server"></a>取消注册 SQL Server

在删除保护之后、删除保管库之前取消注册 SQL 服务器

1. 打开已注册到 SQL 虚拟机的恢复服务保管库。

2. 在保管库菜单的“管理”部分，单击“备份基础结构”。  

   ![恢复数据库保护](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. 在“管理服务器”部分，单击“受保护的服务器”。

   ![恢复数据库保护](./media/backup-azure-sql-database/protected-servers.png)

    此时会打开“受保护的服务器”菜单。 

4. 在“受保护的服务器”菜单中，选择要取消注册的服务器。 若要删除保管库，必须取消注册所有服务器。

5. 在“受保护的服务器”菜单中，右键单击受保护的服务器，并选择“删除”。 

   ![恢复数据库保护](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 备份的详细信息，请参阅备份加密虚拟机的 PowerShell 示例。

> [!div class="nextstepaction"]
> [备份加密 VM](./scripts/backup-powershell-sample-backup-encrypted-vm.md)

---
title: 在 Azure Vm 中的 SQL Server 数据库备份 |Microsoft Docs
description: 了解如何在 Azure Vm 中的 SQL Server 数据库备份
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: sachdevaswati
ms.openlocfilehash: 28577bfc755d80cd479a40b9e2b653af6ddec319
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204459"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>备份 Azure VM 中的 SQL Server 数据库

SQL Server 数据库是需要低恢复点目标 (RPO) 和长期保留的关键工作负荷。 可以备份使用的 Azure 虚拟机 (Vm) 上运行的 SQL Server 数据库[Azure 备份](backup-overview.md)。

本文介绍如何备份到 Azure 备份恢复服务保管库的 Azure VM 运行的 SQL Server 数据库。

本文将介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建并配置保管库。
> * 发现数据库并将备份设置。
> * 为数据库设置自动保护。


## <a name="prerequisites"></a>必备组件

备份 SQL Server 数据库之前，请检查以下条件：

1. 标识或创建[恢复服务保管库](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault)在同一区域或区域设置用作托管的 SQL Server 实例的 VM。
2. 验证 VM 是否[网络连接](backup-sql-server-database-azure-vms.md#establish-network-connectivity)。
3. 请确保 SQL Server 数据库遵循[Azure 备份对数据库命名准则](#database-naming-guidelines-for-azure-backup)。
4. 专用于 SQL 2008 和 2008 R2 中，[添加注册表项](#add-registry-key-to-enable-registration)启用服务器注册。 此步骤将不是需要时的功能是已正式发布。
5. 检查不会为数据库启用任何其他备份解决方案。 备份数据库之前，请禁用所有其他 SQL Server 备份。

> [!NOTE]
> 为 Azure VM 和不会发生冲突的 VM 上运行的 SQL Server 数据库，可以启用 Azure 备份。


### <a name="establish-network-connectivity"></a>建立网络连接

对于所有操作，SQL Server 虚拟机需要连接到 Azure 公共 IP 地址。 （数据库发现配置的备份、 计划备份、 还原恢复点和其他操作） 的 VM 操作失败，而无需连接到 Azure 公共 IP 地址。

建立连接使用下列选项之一：

- **允许 Azure 数据中心 IP 范围**。 此选项允许[IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)的下载中。 若要访问的网络安全组 (NSG)，请使用 Set-azurenetworksecurityrule cmdlet。 如果你是安全收件人列表仅特定于区域的 Ip 中，您还需要更新 Azure Active Directory (Azure AD) 服务标记，若要启用身份验证的安全收件人列表。

- **允许访问使用 NSG 标记**。 如果使用 Nsg 来限制连接，此选项将规则添加到 NSG，允许通过 AzureBackup 标记的 Azure 备份对出站访问。 除了此标记，还将需要对应[规则](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)Azure ad 和 Azure 存储，以允许进行身份验证和数据传输的连接。 仅在 PowerShell 上目前已 AzureBackup 标记。 若要通过使用 AzureBackup 标记创建一个规则：

    - 添加 Azure 帐户凭据和更新国家/地区云<br/>
    `Add-AzureRmAccount`

    - 选择 NSG 订阅<br/>
    `Select-AzureRmSubscription "<Subscription Id>"`

     - 选择 NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

    - 添加允许 Azure 备份服务标记的出站规则<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

  - 保存 NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`
- **允许使用 Azure 防火墙标记访问**。 如果正在使用 Azure 防火墙，创建一个应用程序规则使用 AzureBackup [FQDN 标记](https://docs.microsoft.com/azure/firewall/fqdn-tags)。 这允许对 Azure 备份的出站访问。
- **部署 HTTP 代理服务器将流量路由到**。 Azure VM 上备份 SQL Server 数据库，请在 VM 上的备份扩展使用 HTTPS Api 将管理命令发送到 Azure 备份和到 Azure 存储的数据。 备份扩展也使用 Azure AD 进行身份验证。 通过 HTTP 代理路由这三个服务的备份扩展流量。 该扩展是为了访问公共 Internet 而配置的唯一组件。

连接选项包括以下优点和缺点：

**选项** | **优点** | **缺点**
--- | --- | ---
允许 IP 范围 | 无额外成本 | 若要管理，因为 IP 地址范围会不断变化的复杂 <br/><br/> 提供访问整个 Azure，而不仅仅是 Azure 存储
使用 NSG 服务标记 | 更轻松地管理，因为范围的更改会自动合并 <br/><br/> 无额外成本 <br/><br/> | 可以使用 Nsg 仅使用 <br/><br/> 提供对整个服务的访问
使用 Azure 防火墙 FQDN 标记 | 更轻松地管理为自动托管所需的 Fqdn | 可以在 Azure 防火墙仅
使用 HTTP 代理 | 在代理中的对存储进行精确控制允许 Url <br/><br/> 点 internet 访问 Vm <br/><br/> 不受 Azure IP 地址更改 | 通过代理软件运行 VM 的额外成本

### <a name="database-naming-guidelines-for-azure-backup"></a>数据库为 Azure 备份命名准则

避免在数据库的名称中使用以下元素：

  * 尾随和前导空格
  * 尾随感叹号 （！）
  * 右方括号 (])
  * 以分号;
  * 正斜杠 /

别名可用于不支持的字符，但我们建议避免它们。 有关详细信息，请参阅 [Understanding the Table Service Data Model](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN)（了解表服务数据模型）。

### <a name="add-registry-key-to-enable-registration"></a>添加注册表项以启用注册

1. 打开注册表编辑器
2. 创建注册表目录路径：HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook （您需要创建下 WorkloadBackup 又需要在 Microsoft 下创建 'Key' TestHook）。
3. 在注册表的目录路径下创建一个新的字符串值的字符串名称与**AzureBackupEnableWin2K8R2SP1**和值：**True**

    ![RegEdit 启用注册](media/backup-azure-sql-database/reg-edit-sqleos-bkp.png)

或者，可以通过使用以下命令运行.reg 文件来自动执行此步骤：

```csharp
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook]
"AzureBackupEnableWin2K8R2SP1"="True"
```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>发现 SQL Server 数据库

如何发现 VM 上运行的数据库：

1. 在 [Azure 门户](https://portal.azure.com)中，打开用于备份数据库的恢复服务保管库。

2. 在中**恢复服务保管库**仪表板中选择**备份**。

   ![选择“+备份”打开“备份目标”菜单](./media/backup-azure-sql-database/open-backup-menu.png)

3. 在**备份目标**，请设置**你的工作负荷的运行位置？** 到**Azure**。

4. 在“要备份哪些内容”中，选择“Azure VM 中的 SQL Server”。  

    ![选择“Azure VM 中的 SQL Server”进行备份](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. 在“备份目标” > “发现 VM 中的数据库”中，选择“开始发现”以搜索订阅中不受保护的 VM。    此搜索可能需要一段时间，具体取决于订阅中的未受保护的 Vm 数量。

   - 发现后，未受保护的 VM 应会按名称和资源组列在列表中。
   - 如果 VM 未列出按预期，请参阅是否已备份保管库中。
   - 多个 Vm 可以有相同的名称，但它们将属于不同的资源组。

     ![在搜索 VM 中的数据库期间，备份将会挂起。](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. 在 VM 列表中，选择运行 SQL Server 数据库的VM，然后选择“发现数据库”。 

7. 在跟踪数据库发现**通知**。 此操作所需的时间取决于 VM 数据库的数量。 发现选定的数据库后，会显示一条成功消息。

    ![部署成功消息](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure 备份将发现该 VM 上的所有 SQL Server 数据库。 在发现过程中在后台将发生以下元素：

    - Azure 备份的工作负荷备份到保管库注册 VM。 可以到此保管库备份已注册的 VM 上的所有数据库。
    - Azure 备份会在 VM 上安装 AzureBackupWindowsWorkload 扩展。 在 SQL 数据库上不安装任何代理。
    - Azure 备份在 VM 上创建的服务帐户 NT Service\AzureWLBackupPluginSvc。
      - 所有备份和还原操作使用该服务帐户。
      - NT Service\AzureWLBackupPluginSvc 需要 SQL sysadmin 权限。 在 Marketplace 中创建的所有 SQL Server Vm 都附带安装 SqlIaaSExtension。 AzureBackupWindowsWorkload 扩展使用 SQLIaaSExtension 以自动获取所需的权限。
    - 如果未从 Marketplace 创建 VM 或位于 SQL 2008 和 2008 R2，VM 可能没有安装，SqlIaaSExtension 和发现操作失败，出现错误消息 UserErrorSQLNoSysAdminMembership。 若要解决此问题，请执行下面的说明[设置 VM 权限](backup-azure-sql-database.md#set-vm-permissions)。

        ![选择 VM 和数据库](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>配置备份  

1. 在中**备份目标以后** > **步骤 2:配置备份**，选择**配置备份**。

   ![选择“配置备份”](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. 在中**选择备份的项**，请参阅所有已注册的可用性组和独立 SQL Server 实例。 选择所有不受保护的数据库的列表展开该实例或 Always On 可用性组中某行的左侧的箭头。  

    ![显示包含独立数据库的所有 SQL Server 实例](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. 选择你想要保护，然后选择的所有数据库**确定**。

   ![保护数据库](./media/backup-azure-sql-database/select-database-to-protect.png)

   为了优化备份负载，Azure 备份会将一个备份作业中的最大数据库数目设置为 50。

     * 若要对 50 个以上的数据库提供保护，请配置多个备份。
     * 若要启用[](#enable-auto-protection)整个实例或 Always On 可用性组。 在中**AUTOPROTECT**下拉列表中，选择**ON**，然后选择**确定**。

    > [!NOTE]
    > [自动保护](#enable-auto-protection)功能不仅使所有现有数据库上的保护，但也会自动保护添加到该实例或可用性组的任何新数据库。  

4. 选择**确定**以打开**备份策略**。

    ![启用 Always On 可用性组的自动保护](./media/backup-azure-sql-database/enable-auto-protection.png)

5. 在中**备份策略**，选择一个策略，然后选择 **确定**。

   - 选择作为 HourlyLogBackup 默认策略。
   - 选择前面为 SQL 创建的现有备份策略。
   - 根据 RPO 和保留范围定义新策略。

     ![选择“备份策略”](./media/backup-azure-sql-database/select-backup-policy.png)

6. 在中 **备份**，选择 **启用备份**。

    ![启用选定的备份策略](./media/backup-azure-sql-database/enable-backup-button.png)

7. 在门户的“通知”区域跟踪配置进度 ****  。

    ![通知区域](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>创建备份策略

备份策略定义备份创建时间以及这些备份的保留时间。

- 策略是在保管库级别创建的。
- 多个保管库可以使用相同的备份策略，但必须向每个保管库应用该备份策略。
- 创建备份策略时，每日完整备份是默认设置。
- 可以添加差异备份，但仅在将完整备份配置为每周发生时，才能这样做。
- 了解如何[不同类型的备份策略](backup-architecture.md#sql-server-backup-types)。

创建备份策略：

1. 在保管库中，选择**备份策略** > **添加**。
2. 在中**外**，选择**Azure VM 中 SQL Server**定义的策略类型。

   ![为新的备份策略选择策略类型](./media/backup-azure-sql-database/policy-type-details.png)

3. 在“策略名称”处输入新策略的名称  。
4. 在中**完整备份策略**，选择**备份频率**。 请执行以下某**每日**或**每周**。

   - 如果选择“每日”，请选择备份作业开始时的小时和时区。 
   - 如果选择“每周”，请选择备份作业开始时的星期、小时和时区。 
   - 运行完整备份，因为不能关闭**完整备份**选项。
   - 选择**完整备份**若要查看的策略。
   - 对于每日完整备份，无法创建差异备份。

     ![新备份策略字段](./media/backup-azure-sql-database/full-backup-policy.png)  

5. 在中**期**，默认情况下选择所有选项。 清除任何保留范围限制不想，，然后设置要使用的时间间隔。

    - 任何类型的备份 （完整、 差异备份和日志） 的最小保持期为 7 天。
    - 恢复点已根据其保留范围标记为保留。 例如，如果选择每日完整备份，则每天只触发一次完整备份。
    - 标记并保留特定日期基于备份每周的保持期和每周的保持期设置。
    - 每月和每年保留范围的行为方式类似。

       ![保留范围间隔设置](./media/backup-azure-sql-database/retention-range-interval.png)

6. 在“完整备份策略”菜单中，选择“确定”接受设置。  
7. 若要添加差异备份策略，请选择“差异备份”。 

   ![保留范围间隔设置](./media/backup-azure-sql-database/retention-range-interval.png)
   ![打开差异备份策略菜单](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. 在“差异备份策略”中，选择“启用”打开频率和保留控件。  

    - 可以触发每天只有一个差异备份。
    - 差异备份最多可以保留 180 天。 对于较长的保留期，使用完整备份。

9. 选择“确定”保存策略，并返回“备份策略”主菜单。  

10. 若要添加事务日志备份策略，请选择“日志备份”。 
11. 在“日志备份”中选择“启用”，并设置频率和保留控件。   日志备份可以随时按每隔 15 分钟发生，并可以保留最多 35 天。
12. 选择“确定”保存策略，并返回“备份策略”主菜单。  

    ![编辑日志备份策略](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. 在“备份策略”菜单中，选择是否启用“SQL 备份压缩”。  
    - 默认已禁用压缩。
    - Azure 备份在后端使用 SQL 本机备份压缩。

14. 完成备份策略的编辑后，选择“确定”。 


### <a name="modify-policy"></a>修改策略
修改策略以更改备份频率或保留期范围。

> [!NOTE]
> 在保留期内的任何更改将回顾性地应用于新的除了所有旧的恢复点。

在保管库仪表板中，转到**管理** > **备份策略**，然后选择你想要编辑的策略。

  ![管理备份策略](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>启用自动保护  

可以启用自动保护到独立 SQL Server 实例或 Always On 可用性组自动备份所有现有和将来的数据库。

- 上一次选择为自动保护的数据库数目没有限制。
- 有选择地无法保护或从实例中时启用自动保护的保护中排除的数据库。
- 如果实例已包含一些受保护的数据库，它们将在其各自的策略下，受保护甚至后保留开启自动保护。 添加更高版本的所有未受保护的数据库将具有仅在启用自动保护，下列出的时间定义的单个策略 **配置备份**。 但是，可以更改自动保护的数据库与更高版本关联的策略。  

若要启用自动保护：

  1. 在“要备份的项”中，选择要为其启用自动保护的实例。 
  2. 选择下的下拉列表**AUTOPROTECT**，选择**ON**，然后选择**确定**。

      ![启用可用性组的自动保护](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. 备份是针对所有数据库统一配置的，可以在“备份作业”中跟踪备份。 

如果你需要禁用自动保护，选择下面的实例名称**配置备份**，然后选择**禁用 Autoprotect**的实例。 所有数据库将都继续进行备份，但将来的数据库不会自动受到保护。

![禁用自动保护在该实例上](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>后续步骤

了解如何：

- [还原备份 SQL Server 数据库](restore-sql-database-azure-vm.md)
- [管理备份 SQL Server 数据库](manage-monitor-sql-database-backup.md)

---
title: 使用 Azure 备份将 SAP HANA 数据库备份到 Azure
description: 本文介绍如何使用 Azure 备份服务将 SAP HANA 数据库备份到 Azure 虚拟机。
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: dd4c6fc0e018f3fc8f2a2029ef8a90cdc305e2c2
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76765513"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>备份 Azure VM 中的 SAP HANA 数据库

SAP HANA 数据库是需要低恢复点目标（RPO）和长期保留的关键工作负荷。 可以使用[Azure 备份](backup-overview.md)来备份 Azure 虚拟机（vm）上运行的 SAP HANA 数据库。

本文介绍如何将 Azure Vm 上运行的 SAP HANA 数据库备份到 Azure 备份恢复服务保管库。

本文介绍如何执行以下操作：
> [!div class="checklist"]
>
> * 创建并配置保管库
> * 发现数据库
> * 配置备份
> * 运行按需备份作业

>[!NOTE]
>Azure vm**中的 SQL Server 软删除和 AZURE vm 工作负荷中 SAP HANA 的软删除**现已在预览版中提供。<br>
>若要注册预览版，请在 AskAzureBackupTeam@microsoft.com 写信

## <a name="prerequisites"></a>必备组件

若要设置用于备份的数据库，请参阅[先决条件](tutorial-backup-sap-hana-db.md#prerequisites)和[设置权限](tutorial-backup-sap-hana-db.md#setting-up-permissions)部分。

### <a name="set-up-network-connectivity"></a>设置网络连接

对于所有操作，SAP HANA VM 虚拟机需要与 Azure 公共 IP 地址建立连接。 如果未连接到 Azure 公共 IP 地址，VM 操作（数据库发现、配置备份、计划备份、还原恢复点等）将失败。

使用以下选项之一建立连接：

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>允许 Azure 数据中心 IP 范围

此选项允许已下载文件中的 [IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 若要访问网络安全组 (NSG)，请使用 Set-AzureNetworkSecurityRule cmdlet。 如果安全收件人列表仅包含特定于区域的 IP，则还需更新 Azure Active Directory (Azure AD) 服务标记的安全收件人列表以启用身份验证。

#### <a name="allow-access-using-nsg-tags"></a>允许使用 NSG 标记进行访问

如果使用 NSG 来限制连接，则应使用 AzureBackup 服务标记以允许对 Azure 备份进行出站访问。 此外，还应允许使用 Azure AD 和 Azure 存储的[规则](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)，在连接后进行身份验证和数据传输。 这可以通过 Azure 门户或 PowerShell 来完成。

若要使用门户创建规则，请执行以下操作：

  1. 在“所有服务”中转到“网络安全组”，然后选择“网络安全组”。
  2. 在“设置”下，选择“出站安全规则”。
  3. 选择 **添加** 。 输入创建新规则所需的所有详细信息，如[安全规则设置](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)中所述。 请确保将选项“目标”设置为“服务标记”，将“目标服务标记”设置为 **AzureBackup**。
  4. 单击“添加”，保存新创建的出站安全规则。

若要使用 PowerShell 创建规则，请执行以下操作：

 1. 添加 Azure 帐户凭据并更新国家/地区云<br/>
      `Add-AzureRmAccount`<br/>

 2. 选择 NSG 订阅<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. 选择 NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. 为 Azure 备份服务标记添加允许出站规则<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. 为 Azure 存储服务标记添加允许出站规则<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. 为 AzureActiveDirectory 服务标记添加允许出站规则<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. 保存 NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**允许使用 Azure 防火墙标记进行访问**。 如果使用 Azure 防火墙，请使用 AzureBackup [FQDN 标记](https://docs.microsoft.com/azure/firewall/fqdn-tags)创建应用程序规则。 这允许对 Azure 备份进行出站访问。

**部署用于路由流量的 HTTP 代理服务器**。 在 Azure VM 中备份 SAP HANA 数据库时，该 VM 上的备份扩展将使用 HTTPS API 将管理命令发送到 Azure 备份，并将数据发送到 Azure 存储。 备份扩展还使用 Azure AD 进行身份验证。 通过 HTTP 代理路由这三个服务的备份扩展流量。 该扩展是为了访问公共 Internet 而配置的唯一组件。

连接性选项包括以下优点和缺点：

**选项** | **优点** | **缺点**
--- | --- | ---
允许 IP 范围 | 无额外成本 | 管理起来很复杂，因为 IP 地址范围随时会变化 <br/><br/> 允许访问整个 Azure，而不只是 Azure 存储
使用 NSG 服务标记 | 由于范围更改会自动合并，因此更易于管理 <br/><br/> 无额外成本 <br/><br/> | 仅可与 NSG 配合使用 <br/><br/> 提供对整个服务的访问权限
使用 Azure 防火墙 FQDN 标记 | 自动管理必需的 FQDN，因此更易于管理 | 仅可与 Azure 防火墙配合使用
使用 HTTP 代理 | 允许在代理中对存储 URL 进行精细控制 <br/><br/> 对 VM 进行单点 Internet 访问 <br/><br/> 不受 Azure IP 地址变化的影响 | 通过代理软件运行 VM 带来的额外成本

## <a name="onboard-to-the-public-preview"></a>加入公共预览版

加入公共预览版的方式如下：

* 在门户中，按照[本文](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)所述操作，将订阅 ID 注册到恢复服务服务提供程序。
* 对于 PowerShell 中的 "Az" 模块，运行此 cmdlet。 完成时应显示“已注册”。

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```
* 如果在 PowerShell 中使用 "AzureRM" 模块，请运行此 cmdlet。 完成时应显示“已注册”。

    ```powershell
    Register-AzureRmProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```
    

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>发现数据库

1. 在保管库中，单击“开始使用”中的“备份”。 在“工作负荷在哪里运行?”中，选择“Azure VM 中的 SAP HANA”。
2. 单击“启动发现”。 这会开始在保管库区域中发现未受保护的 Linux VM。

   * 发现后，未受保护的 Vm 会在门户中显示，并按名称和资源组列出。
   * 如果未按预期方式列出某个 VM，请检查它是否已在保管库中备份。
   * 多个 Vm 可以具有相同的名称，但它们属于不同的资源组。

3. 在“选择虚拟机”中，单击脚本下载链接。此脚本可为 Azure 备份服务提供访问 SAP HANA VM 的权限以进行数据库发现。
4. 在要备份 SAP HANA 数据库的每个 VM 上运行该脚本。
5. 在 Vm 上运行脚本后，在 "**选择虚拟机**" 中选择 vm。 然后单击“发现数据库”。
6. Azure 备份可发现该 VM 上的所有 SAP HANA 数据库。 在发现期间，Azure Backup 将 VM 注册到保管库，并在该 VM 上安装扩展。 不会在数据库中安装任何代理。

    ![发现 SAP HANA 数据库](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>配置备份  

现在启用备份。

1. 在步骤2中，单击 "**配置备份**"。

    ![配置备份](./media/backup-azure-sap-hana-database/configure-backup.png)
2. 在 "**选择要备份的项目**" 中，选择要保护的所有数据库 > **"确定"** 。

    ![选择要备份的项](./media/backup-azure-sap-hana-database/select-items.png)
3. 在 "**备份策略** > **选择" 备份策略**"，并按照以下说明为数据库创建新的备份策略。

    ![选择备份策略](./media/backup-azure-sap-hana-database/backup-policy.png)
4. 创建策略后，在 "**备份**" 菜单上单击 "**启用备份**"。

    ![启用备份](./media/backup-azure-sap-hana-database/enable-backup.png)
5. 在门户的“通知”区域跟踪备份配置进度。

### <a name="create-a-backup-policy"></a>创建备份策略

备份策略定义备份创建时间以及这些备份的保留时间。

* 策略是在保管库级别创建的。
* 多个保管库可以使用相同的备份策略，但必须向每个保管库应用该备份策略。

按以下方式指定策略设置：

1. 在“策略名称”处输入新策略的名称。

   ![输入策略名称](./media/backup-azure-sap-hana-database/policy-name.png)
2. 在“完整备份策略”中选择“备份频率”，然后选择“每日”或“每周”。
   * **每天**：选择备份作业开始的小时和时区。
       * 必须运行完整备份。 无法关闭此选项。
       * 单击“完整备份”以查看策略。
       * 对于每日完整备份，无法创建差异备份。
   * "**每周**"：选择运行备份作业的 "星期几"、"小时" 和 "时区"。

   ![选择备份频率](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. 在“保持期”中，对完整备份配置保留设置。
    * 默认情况下，所有选项都处于选中状态。 清除不想使用的任何保持期限制，并设置所需的保留范围限制。
    * 任何备份类型（完整/差异/日志）的最短保持期均为七天。
    * 恢复点已根据其保留范围标记为保留。 例如，如果选择每日完整备份，则每天只触发一次完整备份。
    * 根据每周保持期和设置，将会标记并保留特定日期的备份。
    * 每月和每年保留范围的行为类似。

4. 在“完整备份策略”菜单中，单击“确定”接受设置。
5. 选择 "**差异备份**" 以添加差异策略。
6. 在“差异备份策略”中，选择“启用”打开频率和保留控件。
    * 每天最多可以触发一次差异备份。
    * 差异备份最多可以保留 180 天。 如果需要保留更长时间，必须使用完整备份。

    ![差异备份策略](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > 目前不支持增量备份。

7. 单击“确定”保存策略，并返回“备份策略”主菜单。
8. 请选择“日志备份”，以添加事务日志备份策略。
    * 在 "**日志备份**" 中，选择 "**启用**"。  由于 SAP HANA 管理所有日志备份，此类备份无法被禁用。
    * 设置频率和保持控制。

    > [!NOTE]
    > 日志备份仅在成功完成完整备份后开始流动。

9. 单击“确定”保存策略，并返回“备份策略”主菜单。
10. 完成定义备份策略后，单击“确定”。

> [!NOTE]
> 每个日志备份都链接到上一个完整备份，以形成恢复链。 此完整备份将保留，直到最后一个日志备份的保留期过期。 这可能意味着完整备份会保留一段额外的时间，以确保所有日志都可以恢复。 假设用户具有每周完整备份、每日差异和2小时的日志。 所有这些都将保留30天。 不过，只有在下一次完整备份可用时（即30到7天后），才能真正清除/删除每周的完全备份。 例如，每周的完整备份在11月16日执行。 根据保留策略，它应保留到12月16日。 此完整的最后一次日志备份在11月22日的下一次计划满之前发生。 在此日志可用到 Dec 22 日之前，不能删除11月16日。 因此，11月16日已满，直到 Dec 22 日。

## <a name="run-an-on-demand-backup"></a>运行按需备份

备份按照策略计划运行。 可以按需运行备份，如下所示：

1. 在保管库菜单中，单击“备份项”。
2. 在 "**备份项目**" 中，选择运行 SAP HANA 数据库的 VM，然后单击 "**立即备份**"。
3. 现在，在 "**备份**" 中使用 "日历" 控件选择应该保留恢复点的最后一天。 然后单击“确定”。
4. 监视门户通知。 可以在保管库仪表板 >“备份作业” > “进行中”监视作业进度。 创建初始备份可能需要一些时间，具体取决于数据库的大小。

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>在启用了 Azure 备份的数据库上运行 SAP HANA Studio 备份

如果要使用 Azure 备份进行备份的数据库的本地备份（使用 HANA Studio），请执行以下操作：

1. 等待数据库的所有完整备份或日志备份完成。 检查 SAP HANA Studio/考核中心中的状态。
2. 禁用日志备份，并将备份目录设置为相关数据库的文件系统。
3. 为此，请双击 " **systemdb** > **配置** > **选择" 数据库** > **筛选器（日志）** "。
4. 将**enable_auto_log_backup**设置为 "**否**"。
5. 将**log_backup_using_backint**设置为**False**。
6. 采用数据库的按需完整备份。
7. 等待完整备份和目录备份完成。
8. 将以前的设置还原为适用于 Azure 的设置：
    * 将**enable_auto_log_backup**设置为 **"是"** 。
    * 将**log_backup_using_backint**设置为**True**。

## <a name="next-steps"></a>后续步骤

* 了解如何[还原在 Azure VM 上运行的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* 了解如何[管理使用 Azure Backup 备份的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)

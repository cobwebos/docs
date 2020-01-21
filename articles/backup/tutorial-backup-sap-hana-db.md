---
title: 教程 - 备份 Azure VM 中的 SAP HANA 数据库
description: 在本教程中，了解如何将 Azure VM 上运行的 SAP HANA 数据库备份到 Azure 备份恢复服务保管库。
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: bb84f6b362adf7c190f3300e6e3f1bc572153151
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75753990"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>教程：备份 Azure VM 中的 SAP HANA 数据库

本教程介绍如何将 Azure VM 上运行的 SAP HANA 数据库备份到 Azure 备份恢复服务保管库。 在本文中，你将学习如何：

> [!div class="checklist"]
>
> * 创建并配置保管库
> * 发现数据库
> * 配置备份

[这里](sap-hana-backup-support-matrix.md#scenario-support)有我们目前支持的所有方案。

## <a name="onboard-to-the-public-preview"></a>加入公共预览版

加入公共预览版的方式如下：

* 在门户中，按照[本文](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)所述操作，将订阅 ID 注册到恢复服务服务提供程序。

* 对于 PowerShell，运行此 cmdlet。 完成时应显示“已注册”。

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="prerequisites"></a>必备条件

在配置备份之前，请确保执行以下操作：

1. 在运行 SAP HANA 数据库的 VM 上，使用 zypper 从官方 SLES 包/介质安装并启用 ODBC 驱动程序包，如下所示：

```bash
sudo zypper update
sudo zypper install unixODBC
```

>[!NOTE]
> 如果不打算更新存储库，请确保 unixODBC 处于最低版本 2.3.4。 若要获知 uniXODBC 的版本，请以 root 身份运行 `odbcinst -j`
>

2. 允许从 VM 连接到 Internet，以便它可以访问 Azure，如[以下过程](#set-up-network-connectivity)中所述。

3. 在将以根用户身份安装 HANA 的虚拟机中运行预注册脚本。 [此脚本](https://aka.ms/scriptforpermsonhana)将设置[正确的权限](#setting-up-permissions)。

## <a name="set-up-network-connectivity"></a>设置网络连接

对于所有操作，SAP HANA VM 虚拟机需要与 Azure 公共 IP 地址建立连接。 如果未连接到 Azure 公共 IP 地址，VM 操作（数据库发现、配置备份、计划备份、还原恢复点等）将失败。

使用以下选项之一建立连接：

### <a name="allow-the-azure-datacenter-ip-ranges"></a>允许 Azure 数据中心 IP 范围

此选项允许已下载文件中的 [IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 若要访问网络安全组 (NSG)，请使用 Set-AzureNetworkSecurityRule cmdlet。 如果安全收件人列表仅包含特定于区域的 IP，则还需更新 Azure Active Directory (Azure AD) 服务标记的安全收件人列表以启用身份验证。

### <a name="allow-access-using-nsg-tags"></a>允许使用 NSG 标记进行访问

如果使用 NSG 来限制连接，则应使用 AzureBackup 服务标记以允许对 Azure 备份进行出站访问。 此外，还应允许使用 Azure AD 和 Azure 存储的[规则](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)，在连接后进行身份验证和数据传输。 这可以通过 Azure 门户或 PowerShell 来完成。

若要使用门户创建规则，请执行以下操作：

  1. 在“所有服务”  中转到“网络安全组”  ，然后选择“网络安全组”。
  2. 在“设置”下，选择“出站安全规则”。  
  3. 选择 **添加** 。 输入创建新规则所需的所有详细信息，如[安全规则设置](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)中所述。 请确保将选项“目标”  设置为“服务标记”  ，将“目标服务标记”  设置为 **AzureBackup**。
  4. 单击“添加”  ，保存新创建的出站安全规则。

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

## <a name="setting-up-permissions"></a>设置权限

此预注册脚本可执行以下操作：

1. 在 HANA 系统中创建 AZUREWLBACKUPHANAUSER，并添加以下必需角色和权限：
   * 数据库管理员：在还原期间创建新数据库。
   * 目录读取：读取备份目录。
   * SAP_INTERNAL_HANA_SUPPORT：访问一些专用表。
2. 向 HANA 插件的 Hdbuserstore 添加密钥，以便处理所有操作（数据库查询、还原操作、配置和运行备份）。

若要确认创建密钥，请在具有 SIDADM 凭据的 HANA 计算机上运行以下 HDBSQL 命令：

```hdbsql
hdbuserstore list
```

命令输出应显示 {SID}{DBNAME} 密钥，用户显示为 AZUREWLBACKUPHANAUSER。

>[!NOTE]
> 请确保 /usr/sap/{SID}/home/.hdb/ 下有一组唯一的 SSFS 文件。 此路径中应只有一个文件夹。
>

## <a name="create-a-recovery-service-vault"></a>创建恢复服务保管库

恢复服务保管库是用于存储在不同时间创建的备份和恢复点的实体。 恢复服务保管库还包含与受保护虚拟机关联的备份策略。

若要创建恢复服务保管库，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com/)中登录到自己的订阅。

2. 在左侧菜单中，选择“所有服务” 

![选择“所有服务”](./media/tutorial-backup-sap-hana-db/all-services.png)

3. 在“所有服务”对话框中，输入“恢复服务”   。 资源列表根据输入进行筛选。 在资源列表中，选择“恢复服务保管库”  。

![选择恢复服务保管库](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. 在“恢复服务保管库”仪表板上，选择“添加”   。

![添加恢复服务保管库](./media/tutorial-backup-sap-hana-db/add-vault.png)

此时会打开“恢复服务保管库”对话框  。 提供“名称”、“订阅”、“资源组”和“位置”的值  

![创建恢复服务保管库](./media/tutorial-backup-sap-hana-db/create-vault.png)

* **Name**：此名称用于标识恢复服务保管库，并且必须对于 Azure 订阅是唯一的。 指定的名称应至少包含 2 个字符，最多不超过 50 个字符。 名称必须以字母开头且只能包含字母、数字和连字符。 对于本教程，我们使用了名称“SAPHanaVault”  。
* **订阅**：选择要使用的订阅。 如果你仅是一个订阅的成员，则会看到该名称。 如果不确定要使用哪个订阅，请使用默认的（建议的）订阅。 仅当工作或学校帐户与多个 Azure 订阅关联时，才会显示多个选项。 本教程中，我们使用了“SAP HANA 解决方案实验室订阅”订阅  。
* **资源组**：使用现有资源组，或创建一个新的资源组。 本教程中，我们使用了“SAPHANADemo”  。<br>
要查看订阅中可用的资源组列表，请选择“使用现有资源”  ，然后从下拉列表框中选择一个资源。 若要创建新资源组，请选择“新建”，然后输入名称  。 有关资源组的完整信息，请参阅 [Azure 资源管理器概述](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。
* **位置**：为保管库选择地理区域。 保管库必须与运行 SAP HANA 的虚拟机位于同一区域中。 我们使用了“美国东部 2”  。

5. 选择“查看 + 创建”  。

   ![选择“查看和创建”](./media/tutorial-backup-sap-hana-db/review-create.png)

现已创建恢复服务保管库。

## <a name="discover-the-databases"></a>发现数据库

1. 在保管库中，单击“开始使用”中的“备份”   。 在“工作负荷在哪里运行?”中，选择“Azure VM 中的 SAP HANA”   。
2. 单击“启动发现”  。 这会开始在保管库区域中发现未受保护的 Linux VM。 你将看到要保护的 Azure VM。
3. 在“选择虚拟机”中，单击脚本下载链接。此脚本可为 Azure 备份服务提供访问 SAP HANA VM 的权限以进行数据库发现  。
4. 在托管要备份的 SAP HANA 数据库的 VM 上运行此脚本。
5. 在 VM 上运行此脚本后，在“选择虚拟机”中选择该 VM  。 然后单击“发现数据库”  。
6. Azure 备份可发现该 VM 上的所有 SAP HANA 数据库。 在发现期间，Azure Backup 将 VM 注册到保管库，并在该 VM 上安装扩展。 不会在数据库中安装任何代理。

   ![发现数据库](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>配置备份

发现我们要备份的数据库之后，现在可以启用备份。

1. 单击“配置备份”  。

![配置备份](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. 在“选择要备份的项”中，选择一个或多个要保护的数据库，然后单击“确定”   。

![选择要备份的项](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. 在“备份策略”>“选择备份策略”中，按照下一部分中的说明，为数据库创建一个新的备份策略  。

![选择备份策略](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. 创建策略后，在“备份”菜单中单击“启用备份”   。

   ![单击启用备份](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. 在门户的“通知”区域跟踪备份配置进度  。

## <a name="creating-a-backup-policy"></a>创建备份策略

备份策略定义备份创建时间以及这些备份的保留时间。

* 策略是在保管库级别创建的。
* 多个保管库可以使用相同的备份策略，但必须向每个保管库应用该备份策略。

按以下方式指定策略设置：

1. 在“策略名称”处输入新策略的名称  。 对于本例，请输入“SAPHANA”  。

![输入新策略的名称](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. 在“完整备份策略”中选择“备份频率”   。 可以选择“每日”或“每周”   。 对于本教程，我们选择了“每日”备份  。

![选择备份频率](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. 在“保持期”中，对完整备份配置保留设置  。
   * 默认情况下，选择所有选项。 清除你不想使用的所有保持期限制，并设置要使用的选项。
   * 任何备份类型（完整/差异/日志）的最短保持期均为七天。
   * 恢复点已根据其保留范围标记为保留。 例如，如果选择每日完整备份，则每天只触发一次完整备份。
   * 根据每周保持期和设置，将会标记并保留特定日期的备份。
   * 每月和每年保留范围的行为类似。
4. 在“完整备份策略”菜单中，单击“确定”接受设置   。
5. 然后选择“差异备份”，以添加差异策略  。
6. 在“差异备份策略”中，选择“启用”打开频率和保留控件。   我们在每个星期日的凌晨 2:00 启用了差异备份，保持期为 30 天    。

   ![差异备份策略](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

>[!NOTE]
>目前不支持增量备份。
>

7. 单击“确定”保存策略，并返回“备份策略”主菜单   。
8. 请选择“日志备份”，以添加事务日志备份策略  。
   * “日志备份”默认设为“启用”   。 由于 SAP HANA 管理所有日志备份，此类备份无法被禁用。
   * 我们已将备份计划设置为 2 小时，保持期为 15 天   。

    ![日志备份策略](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

>[!NOTE]
> 日志备份仅在成功完成一次完整备份之后进行。
>

9. 单击“确定”保存策略，并返回“备份策略”主菜单   。
10. 完成定义备份策略后，单击“确定”  。

现已成功为 SAP HANA 数据库配置备份。

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Azure VM 上运行的 SAP HANA 数据库上运行按需备份](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* 了解如何[还原在 Azure VM 上运行的 SAP HANA 数据库](sap-hana-db-restore.md)
* 了解如何[管理使用 Azure 备份进行备份的 SAP HANA 数据库](sap-hana-db-manage.md)
* 了解[关于 SAP HANA 数据库备份常见问题的疑难解答](backup-azure-sap-hana-database-troubleshoot.md)

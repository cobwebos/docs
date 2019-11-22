---
title: 使用 Azure 备份将 SAP HANA 数据库备份到 Azure
description: 本文介绍如何使用 Azure 备份服务将 SAP HANA 数据库备份到 Azure 虚拟机。
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: ed47f18c9dabc685d6fbe02804562ef86a93190a
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285812"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>在 Azure Vm 中备份 SAP HANA 数据库

SAP HANA 数据库是需要低恢复点目标（RPO）和长期保留的关键工作负荷。 可以使用[Azure 备份](backup-overview.md)来备份 Azure 虚拟机（vm）上运行的 SAP HANA 数据库。

本文介绍如何将 Azure Vm 上运行的 SAP HANA 数据库备份到 Azure 备份恢复服务保管库。

本文介绍如何执行以下操作：
> [!div class="checklist"]
>
> * 创建和配置保管库
> * 发现数据库
> * 配置备份
> * 运行按需备份作业

## <a name="prerequisites"></a>先决条件

若要设置用于备份的数据库，请参阅[先决条件](tutorial-backup-sap-hana-db.md#prerequisites)和[设置权限](tutorial-backup-sap-hana-db.md#setting-up-permissions)部分。

### <a name="set-up-network-connectivity"></a>设置网络连接

对于所有操作，SAP HANA VM 需要连接到 Azure 公共 IP 地址。 VM 操作（数据库发现、配置备份、计划备份、还原恢复点等）在没有连接的情况下无法正常工作。 通过允许访问 Azure 数据中心 IP 范围建立连接：

* 可以下载 Azure 数据中心的[IP 地址范围](https://www.microsoft.com/download/details.aspx?id=41653)，然后允许访问这些 ip 地址。
* 如果你使用的是网络安全组（Nsg），则可以使用 AzureCloud[服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)允许所有 AZURE 公共 IP 地址。 你可以使用[set-azurenetworksecurityrule cmdlet](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0)来修改 NSG 规则。
* 应将端口443添加到允许列表，因为传输是通过 HTTPS 传输的。

## <a name="onboard-to-the-public-preview"></a>集成到公共预览版

加入公共预览版，如下所示：

* 在门户中，按照[本文](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)所述，将订阅 ID 注册到恢复服务提供程序。
* 对于 PowerShell，运行此 cmdlet。 它应作为 "已注册" 完成。

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>发现数据库

1. 在保管库中的**入门**中，单击 "**备份**"。 在**工作负荷的运行位置**，选择 " **SAP HANA 在 Azure VM 中**"。
2. 单击 "**启动发现**"。 这会在保管库区域中启动未受保护的 Linux Vm 的发现。

   * 发现后，未受保护的 Vm 会在门户中显示，并按名称和资源组列出。
   * 如果未按预期方式列出某个 VM，请检查它是否已在保管库中备份。
   * 多个 Vm 可以具有相同的名称，但它们属于不同的资源组。

3. 在 "**选择虚拟机**" 中，单击链接下载脚本，该脚本提供 Azure 备份服务访问用于数据库发现的 SAP HANA vm 的权限。
4. 在要备份 SAP HANA 数据库的每个 VM 上运行该脚本。
5. 在 Vm 上运行脚本后，在 "**选择虚拟机**" 中选择 vm。 然后单击 "**发现**数据库"。
6. Azure 备份会发现 VM 上的所有 SAP HANA 数据库。 在发现过程中，Azure 备份会将 VM 注册到保管库，并在 VM 上安装扩展。 数据库上未安装代理。

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
5. 在门户的 "**通知**" 区域中跟踪备份配置进度。

### <a name="create-a-backup-policy"></a>创建备份策略

备份策略定义何时进行备份，以及保留备份的时间。

* 策略是在保管库级别创建的。
* 多个保管库可以使用相同的备份策略，但必须向每个保管库应用该备份策略。

指定策略设置，如下所示：

1. 在“策略名称”处输入新策略的名称。

   ![输入策略名称](./media/backup-azure-sap-hana-database/policy-name.png)
2. 在“完整备份策略”中选择“备份频率”，然后选择“每日”或“每周”。
   * **每天**：选择备份作业开始的小时和时区。
       * 必须运行完整备份。 无法关闭此选项。
       * 单击“完整备份”以查看策略。
       * 对于每日完整备份，无法创建差异备份。
   * "**每周**"：选择运行备份作业的 "星期几"、"小时" 和 "时区"。

   ![选择备份频率](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. 在 "**保持期**" 中，配置完整备份的保持期设置。
    * 默认情况下，所有选项都处于选中状态。 清除不想使用的任何保持期限制，并设置所需的保留范围限制。
    * 任何类型备份的最小保持期（完整/差异/日志）为7天。
    * 恢复点已根据其保留范围标记为保留。 例如，如果选择每日完整备份，则每天只触发一次完整备份。
    * 特定日的备份根据每周保持期和设置进行标记和保留。
    * 每月和每年保留范围的行为类似。

4. 在 "**完整备份策略**" 菜单中，单击 **"确定"** 以接受设置。
5. 选择 "**差异备份**" 以添加差异策略。
6. 在“差异备份策略”中，选择“启用”打开频率和保留控件。
    * 每天最多可以触发一次差异备份。
    * 差异备份最多可以保留 180 天。 如果需要保留更长时间，必须使用完整备份。

    ![差异备份策略](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > 当前不支持增量备份。

7. 单击 **"确定"** 以保存策略并返回到 "主**备份策略**" 菜单。
8. 选择 "**日志备份**" 以添加事务日志备份策略。
    * 在 "**日志备份**" 中，选择 "**启用**"。  由于 SAP HANA 管理所有日志备份，无法禁用此功能。
    * 设置频率和保持控制。

    > [!NOTE]
    > 日志备份仅在成功完成完整备份后开始流动。

9. 单击 **"确定"** 以保存策略并返回到 "主**备份策略**" 菜单。
10. 完成定义备份策略后，单击 **"确定"** 。

## <a name="run-an-on-demand-backup"></a>运行按需备份

备份按照策略计划运行。 可以按需运行备份，如下所示：

1. 在保管库菜单中，单击“备份项”。
2. 在 "**备份项目**" 中，选择运行 SAP HANA 数据库的 VM，然后单击 "**立即备份**"。
3. 在“立即备份”中，使用日历控件选择恢复点的最后保留日期。 然后单击“确定”。
4. 监视门户通知。 可以在保管库仪表板 >“备份作业” **“进行中”监视作业进度。**  >  创建初始备份可能需要一些时间，具体取决于数据库的大小。

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

* 了解如何[还原在 Azure vm 上运行的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* 了解如何[管理使用 Azure Backup 备份的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)

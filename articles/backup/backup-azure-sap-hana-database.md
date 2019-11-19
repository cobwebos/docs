---
title: 将 SAP HANA 数据库备份到 Azure
description: 本教程介绍如何使用 Azure 备份服务将 SAP HANA 数据库备份到 Azure。
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 519e47c6b6793c638e64c4e4bcc4fafdb678c9fb
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172729"
---
# <a name="back-up-an-sap-hana-database-to-azure"></a>将 SAP HANA 数据库备份到 Azure

[Azure 备份](backup-overview.md)支持将 SAP HANA 数据库备份到 Azure。

> [!NOTE]
> 此功能目前处于公开预览状态。 它当前未准备就绪，不具备保证的 SLA。

## <a name="scenario-support"></a>方案支持

**支持** | **详细信息**
--- | ---
**支持的地理区域** | 澳大利亚东南部、澳大利亚东部 <br> 巴西南部 <br> 加拿大中部、加拿大东部 <br> 南东亚，东亚 <br> 美国东部、美国东部2、美国中部、美国西部、美国西部2、美国中北部、美国中部、美国中南部<br> 印度中部、印度南部 <br> 日本东部、日本西部<br> 韩国中部、韩国南部 <br> 欧洲北部、欧洲西部 <br> 英国南部，英国西部
**支持的 VM 操作系统** | SLES 12 SP2、SP3 或 SP4。
**支持的 HANA 版本** | Hana 1.x 上的 SDC，HANA 2.x 上的 MDC < = SPS04 Rev 43

### <a name="current-limitations"></a>当前限制

- 只能备份在 Azure Vm 上运行的 SAP HANA 数据库。
- 只能备份在单个 Azure 虚拟机中运行 SAP HANA 实例。 当前不支持同一 Azure VM 中的多个 HANA 实例。
- 你只能在向上扩展模式下备份数据库。 横向扩展，即目前不支持备份多个 Azure Vm 上的 HANA 实例。
- 你不能在扩展服务器中通过动态分层备份 SAP HANA 实例，即在另一个节点上存在动态分层。 这实质上是不受支持的扩展。
- 不能在同一服务器中为启用了动态分层的 SAP HANA 实例进行备份。 当前不支持动态分层。
- 只能在 Azure 门户中配置 SAP HANA 备份。 此功能不能通过 PowerShell、CLI 进行配置。
- 可以每隔15分钟备份一次数据库日志。 日志备份仅在数据库的成功完整备份完成后开始流动。
- 可以进行完整备份和差异备份。 当前不支持增量备份。
- 应用 SAP HANA 备份后，无法修改备份策略。 如果要使用不同的设置进行备份，请创建新策略或分配其他策略。
  - 若要创建新策略，请在保管库中单击 "**策略**" > "**备份策略**" >  **+** **在 Azure VM 中**添加 > SAP HANA，并指定策略设置。
  - 若要分配不同的策略，请在运行数据库的 VM 的属性中，单击 "当前策略名称"。 然后，在 "**备份策略**" 页上，可以选择用于备份的其他策略。

## <a name="prerequisites"></a>先决条件

在配置备份之前，请确保执行以下操作：

1. 在运行 SAP HANA 数据库的 VM 上，使用 zypper 从官方 SLES 包/媒体安装并启用 ODBC 驱动程序包，如下所示：

    ```unix
    sudo zypper update
    sudo zypper install unixODBC
    ```

    > [!NOTE]
    > 如果不更新存储库，请确保 unixODBC 的版本为 min 2.3.4。 若要了解 uniXODBC 的版本，请以 root 身份运行 ```odbcinst -j```

2. 允许从 VM 连接到 internet，以便它可以访问 Azure，如[以下](#set-up-network-connectivity)过程中所述。

3. 在安装了 HANA 的虚拟机中运行预注册脚本，作为根用户。 此脚本在[门户](#discover-the-databases)中的流中提供，并且需要设置[正确的权限](backup-azure-sap-hana-database-troubleshoot.md#setting-up-permissions)。

### <a name="set-up-network-connectivity"></a>设置网络连接

对于所有操作，SAP HANA VM 需要连接到 Azure 公共 IP 地址。 VM 操作（数据库发现、配置备份、计划备份、还原恢复点等）在没有连接的情况下无法正常工作。 通过允许访问 Azure 数据中心 IP 范围建立连接： 

- 可以下载 Azure 数据中心的[IP 地址范围](https://www.microsoft.com/download/details.aspx?id=41653)，然后允许访问这些 ip 地址。
- 如果你使用的是网络安全组（Nsg），则可以使用 AzureCloud[服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)允许所有 AZURE 公共 IP 地址。 你可以使用[set-azurenetworksecurityrule cmdlet](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0)来修改 NSG 规则。
- 443端口应列入允许列表，因为传输是通过 HTTPS 传输的。

## <a name="onboard-to-the-public-preview"></a>集成到公共预览版

加入公共预览版，如下所示：

- 在门户中，按照[本文](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)所述，将订阅 ID 注册到恢复服务提供程序。 
- 对于 PowerShell，运行此 cmdlet。 它应作为 "已注册" 完成。

    ```powershell
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>发现数据库

1. 在保管库中的**入门**中，单击 "**备份**"。 在**工作负荷的运行位置**，选择 " **SAP HANA 在 Azure VM 中**"。
2. 单击 "**启动发现**"。 这会在保管库区域中启动未受保护的 Linux Vm 的发现。

   - 发现后，未受保护的 Vm 会在门户中显示，并按名称和资源组列出。
   - 如果未按预期方式列出某个 VM，请检查它是否已在保管库中备份。
   - 多个 Vm 可以具有相同的名称，但它们属于不同的资源组。

3. 在 "**选择虚拟机**" 中，单击链接下载脚本，该脚本提供 Azure 备份服务访问用于数据库发现的 SAP HANA vm 的权限
4. 在要备份 SAP HANA 数据库的每个 VM 上运行该脚本。
5. 在 Vm 上运行脚本后，在 "**选择虚拟机**" 中选择 vm。 然后单击 "**发现**数据库"。
6. Azure 备份会发现 VM 上的所有 SAP HANA 数据库。 在发现过程中，Azure 备份会将 VM 注册到保管库，并在 VM 上安装扩展。 数据库上未安装代理。

    ![发现 SAP HANA 数据库](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>配置备份  

现在启用备份。

1. 在步骤2中，单击 "**配置备份**"。
2. 在 "**选择要备份的项目**" 中，选择要保护的所有数据库 > **"确定"** 。
3. 在 "**备份策略** > **选择" 备份策略**"，并按照以下说明为数据库创建新的备份策略。
4. 创建策略后，在 "**备份**" 菜单上单击 "**启用备份**"。
5. 在门户的 "**通知**" 区域中跟踪备份配置进度。

### <a name="create-a-backup-policy"></a>创建备份策略

备份策略定义何时进行备份，以及保留备份的时间。

- 策略是在保管库级别创建的。
- 多个保管库可以使用相同的备份策略，但必须向每个保管库应用该备份策略。

指定策略设置，如下所示：

1. 在“策略名称”处输入新策略的名称。
2. 在“完整备份策略”中选择“备份频率”，然后选择“每日”或“每周”。
   - **每天**：选择备份作业开始的小时和时区。
   
       - 必须运行完整备份。 无法关闭此选项。
       - 单击“完整备份”以查看策略。
       - 对于每日完整备份，无法创建差异备份。
       
   - "**每周**"：选择运行备份作业的 "星期几"、"小时" 和 "时区"。
3. 在 "**保持期**" 中，配置完整备份的保持期设置。
    - 默认情况下，所有选项都处于选中状态。 清除不想使用的任何保持期限制，并设置所需的保留范围限制。
    - 任何类型备份的最小保持期（完整/差异/日志）为7天。
    - 恢复点已根据其保留范围标记为保留。 例如，如果选择每日完整备份，则每天只触发一次完整备份。
    - 特定日的备份根据每周保持期和设置进行标记和保留。
    - 每月和每年保留范围的行为类似。

4. 在 "**完整备份策略**" 菜单中，单击 **"确定"** 以接受设置。
5. 选择 "**差异备份**" 以添加差异策略。
6. 在“差异备份策略”中，选择“启用”打开频率和保留控件。
    - 每天最多可以触发一次差异备份。
    - 差异备份最多可以保留 180 天。 如果需要保留更长时间，必须使用完整备份。

    > [!NOTE]
    > 当前不支持增量备份。 

7. 单击 **"确定"** 以保存策略并返回到 "主**备份策略**" 菜单。
8. 选择 "**日志备份**" 以添加事务日志备份策略。
    - 在 "**日志备份**" 中，选择 "**启用**"。
    - 设置频率和保持控制。

    > [!NOTE]
    > 日志备份仅在成功完成完整备份后开始流动。

9. 单击 **"确定"** 以保存策略并返回到 "主**备份策略**" 菜单。
10. 完成定义备份策略后，单击 **"确定"** 。


## <a name="run-an-on-demand-backup"></a>运行按需备份

备份按照策略计划运行。 可以按需运行备份，如下所示：


1. 在保管库菜单中，单击“备份项”。
2. 在 "**备份项目**" 中，选择运行 SAP HANA 数据库的 VM，然后单击 "**立即备份**"。
3. 在“立即备份”中，使用日历控件选择恢复点的最后保留日期。 然后单击“确定”。
4. 监视门户通知。 你可以在保管库仪表板中监视作业进度 >**备份作业**> 正在进行。 创建初始备份可能需要一些时间，具体取决于数据库的大小。

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>在启用了 Azure 备份的数据库上运行 SAP HANA Studio 备份

如果要使用 Azure 备份进行备份的数据库的本地备份（使用 HANA Studio），请执行以下操作：

1. 等待数据库的所有完整备份或日志备份完成。 在 SAP HANA Studio 中检查状态。
2. 禁用日志备份，并将备份目录设置为相关数据库的文件系统。
3. 为此，请双击 " **systemdb** > **配置** > **选择" 数据库** > **筛选器（日志）** "。
4. 将**enable_auto_log_backup**设置为 "**否**"。
5. 将**log_backup_using_backint**设置为**False**。
6. 对数据库进行临时完整备份。
7. 等待完整备份和目录备份完成。
8. 将以前的设置还原为适用于 Azure 的设置：
    - 将**enable_auto_log_backup**设置为 **"是"** 。
    - 将**log_backup_using_backint**设置为**True**。


## <a name="upgrading-protected-10-dbs-to-20"></a>将受保护的1.0 数据库升级到2。0

如果要保护 SAP HANA 1.0 数据库，并希望升级到2.0，请执行下面所述的步骤。

- 停止保护旧的 SDC DB 的保留数据。
- 重新运行预注册脚本，其详细信息为（sid 和 mdc）。 
- 重新注册扩展（备份 > 视图详细信息-> 选择相关的 Azure VM > 重新注册）。 
- 单击 "重新发现同一 VM 的数据库"。 这会在步骤2中显示新的数据库，其中包含正确的详细信息（SYSTEMDB 和租户 DB，而不是 SDC）。 
- 保护这些新数据库。

## <a name="next-steps"></a>后续步骤

[了解](backup-azure-sap-hana-database-troubleshoot.md)如何在 Azure vm 中使用 SAP HANA 备份时排查常见错误。
[了解如何](backup-azure-arm-vms-prepare.md)备份 Azure vm。

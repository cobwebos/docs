---
title: 备份 Azure VM 中的 SQL Server 数据库
description: 本文介绍如何使用 Azure 备份来备份 Azure 虚拟机上的 SQL Server 数据库。
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 3fd94dc6332d96f875c164dfeadff3a8ab2cad4e
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715590"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>备份 Azure VM 中的 SQL Server 数据库

SQL Server 数据库是需要低恢复点目标 (RPO) 和长期保留的关键工作负荷。 可以使用 [Azure 备份](backup-overview.md)来备份 Azure 虚拟机上运行的 SQL Server 数据库。

本文介绍如何将 Azure VM 上运行的 SQL Server 数据库备份到 Azure 备份恢复服务保管库。

本文将指导如何进行以下操作：

> [!div class="checklist"]
>
> * 创建并配置保管库。
> * 发现数据库并设置备份。
> * 为数据库设置自动保护。

>[!NOTE]
>Azure VM 中的 SQL Server 软删除和 Azure VM 工作负荷中的 SAP HANA 软删除现以预览版形式提供。<br>
>若要注册预览版，请通过 AskAzureBackupTeam@microsoft.com 联系我们

## <a name="prerequisites"></a>先决条件

在备份 SQL Server 数据库之前，请检查以下条件：

1. 在托管 SQL Server 实例的 VM 所在的同一区域和订阅中标识或创建一个[恢复服务保管库](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault)。
2. 验证 VM 是否已建立[网络连接](backup-sql-server-database-azure-vms.md#establish-network-connectivity)。
3. 确认 SQL Server 数据库是否遵循 [Azure 备份的数据库命名准则](#database-naming-guidelines-for-azure-backup)。
4. 检查是否未为该数据库启用了其他任何备份解决方案。 在备份数据库之前，请禁用其他所有 SQL Server 备份。

> [!NOTE]
> 可以同时针对某个 Azure VM 以及该 VM 上运行的 SQL Server 数据库启用 Azure 备份，而不会发生任何冲突。

### <a name="establish-network-connectivity"></a>建立网络连接

对于所有操作，SQL Server VM 需要连接到 Azure 备份服务、Azure 存储和 Azure Active Directory。 这可以通过使用专用终结点，或允许访问所需的公共 IP 地址或 FQDN 来实现。 如果不允许正确连接到所需的 Azure 服务，则可能会导致诸如数据库发现、配置备份、执行备份和还原数据等操作失败。

下表列出了可用于建立连接的各种备选方案：

| **选项**                        | **优点**                                               | **缺点**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 专用终结点                 | 允许通过虚拟网络中的专用 IP 进行备份  <br><br>   提供网络和保管库端的精细控制 | 产生标准专用终结点[开销](https://azure.microsoft.com/pricing/details/private-link/) |
| NSG 服务标记                  | 由于范围更改会自动合并，因此更易于管理   <br><br>   无额外成本 | 仅可与 NSG 配合使用  <br><br>    提供对整个服务的访问权限 |
| Azure 防火墙 FQDN 标记          | 自动管理必需的 FQDN，因此更易于管理 | 仅可与 Azure 防火墙配合使用                         |
| 允许访问服务 FQDN/IP | 无额外成本   <br><br>  适用于所有网络安全设备和防火墙 | 可能需要访问一组广泛的 IP 或 FQDN   |
| 使用 HTTP 代理                 | 对 VM 进行单点 Internet 访问                       | 通过代理软件运行 VM 带来的额外成本         |

关于使用这些选项的更多细节如下：

#### <a name="private-endpoints"></a>专用终结点

使用专用终结点，可以从虚拟网络内的服务器安全地连接到恢复服务保管库。 专用终结点为保管库使用 VNET 地址空间中的 IP。 虚拟网络中的资源与保管库之间的网络流量将通过虚拟网络和 Microsoft 主干网络上的专用链接传输。 这样就不会从公共 Internet 泄露信息。 在[此处](https://docs.microsoft.com/azure/backup/private-endpoints)详细了解 Azure 备份的专用终结点。

#### <a name="nsg-tags"></a>NSG 标记

如果使用网络安全组 (NSG)，请使用 AzureBackup 服务标记以允许对 Azure 备份进行出站访问。 除了 Azure 备份标记外，还需要通过为 Azure AD 和 Azure 存储创建类似的 [NSG 规则](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)，以便在连接后进行身份验证和数据传输。  以下步骤介绍了为 Azure 备份标记创建规则的过程：

1. 在“所有服务”中转到“网络安全组”，然后选择“网络安全组”。

1. 在“设置”下，选择“出站安全规则”。 

1. 选择 **添加** 。 输入创建新规则所需的所有详细信息，如[安全规则设置](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)中所述。 请确保将选项“目标”设置为“服务标记”，将“目标服务标记”设置为“AzureBackup”。

1. 单击“添加”，保存新创建的出站安全规则。

同样，可以为 Azure 存储和 Azure AD 创建 NSG 出站安全规则。

#### <a name="azure-firewall-tags"></a>Azure 防火墙标记

如果使用 Azure 防火墙，请使用 *AzureBackup* [Azure 防火墙 FQDN 标记](https://docs.microsoft.com/azure/firewall/fqdn-tags)创建应用程序规则。 这允许对 Azure 备份进行所有出站访问。

#### <a name="allow-access-to-service-ip-ranges"></a>允许访问服务 IP 范围

如果选择允许访问服务 IP，请参阅[此处](https://www.microsoft.com/download/confirmation.aspx?id=56519)的 JSON 文件中的 IP 范围。 你需要允许访问与 Azure 备份、Azure 存储和 Azure Active Directory 对应的 IP。

#### <a name="allow-access-to-service-fqdns"></a>允许访问服务 FQDN

还可以使用以下 FQDN 以允许从服务器访问所需的服务：

| 服务    | 要访问的域名                             |
| -------------- | ------------------------------------------------------------ |
| Azure 备份  | `*.backup.windowsazure.com`                             |
| Azure 存储 | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | 根据[这篇文章](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)，允许访问第 56 和 59 节下的 FQDN |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>使用 HTTP 代理服务器路由流量

在 Azure VM 中备份 SQL Server 数据库时，该 VM 上的备份扩展将使用 HTTPS API 将管理命令发送到 Azure 备份，并将数据发送到 Azure 存储。 备份扩展还使用 Azure AD 进行身份验证。 通过 HTTP 代理路由这三个服务的备份扩展流量。 使用上面提到的 IP 和 FQDN 列表，以允许访问所需的服务。 不支持已经过身份验证的代理服务器。

### <a name="database-naming-guidelines-for-azure-backup"></a>适用于 Azure 备份的数据库命名准则

避免在数据库名称中使用以下元素：

* 首尾空格
* 尾随感叹号 (!)
* 右方括号 (])
* 分号“;”
* 正斜杠“/”

不支持的字符可以使用别名，但建议避免使用别名。 有关详细信息，请参阅 [Understanding the Table Service Data Model](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)（了解表服务数据模型）。

>[!NOTE]
>对于名称中包含特殊字符（如“+”或“&”）的数据库，不支持“配置保护”操作。 可以更改数据库名称，或启用“自动保护”，这样就可以成功保护这些数据库。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>发现 SQL Server 数据库

如何发现 VM 上运行的数据库：

1. 在 [Azure 门户](https://portal.azure.com)中，打开用于备份数据库的恢复服务保管库。

2. 在“恢复服务保管库”仪表板中，选择“备份”。

   ![选择“+备份”打开“备份目标”菜单](./media/backup-azure-sql-database/open-backup-menu.png)

3. 在“备份目标”中，将“工作负载的运行位置”设置为“Azure”。

4. 在“要备份哪些内容”中，选择“Azure VM 中的 SQL Server”。 

    ![选择“Azure VM 中的 SQL Server”进行备份](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. 在“备份目标” > “发现 VM 中的数据库”中，选择“开始发现”以搜索订阅中不受保护的 VM。   此搜索过程可能需要一些时间，具体取决于订阅中未受保护的 VM 数量。

   * 发现后，未受保护的 VM 应会按名称和资源组列在列表中。
   * 如果某个 VM 未按预期列出，请检查它是否已在保管库中备份。
   * 可能有多个 VM 同名，但它们属于不同的资源组。

     ![在搜索 VM 中的数据库期间，备份将会挂起。](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. 在 VM 列表中，选择运行 SQL Server 数据库的VM，然后选择“发现数据库”。

7. 在“通知”中跟踪数据库发现。 此操作所需的时间取决于 VM 数据库的数量。 发现选定的数据库后，会显示一条成功消息。

    ![部署成功消息](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure 备份将发现该 VM 上的所有 SQL Server 数据库。 在发现期间，后台将出现以下元素：

    * Azure 备份将 VM 注册到用于备份工作负荷的保管库。 已注册 VM 上的所有数据库只能备份到此保管库。
    * Azure 备份在 VM 上安装 AzureBackupWindowsWorkload 扩展。 不会在 SQL 数据库中安装任何代理。
    * Azure 备份在 VM 上创建服务帐户 NT Service\AzureWLBackupPluginSvc。
      * 所有备份和还原操作使用该服务帐户。
      * NT Service\AzureWLBackupPluginSvc 需要 SQL sysadmin 权限。 在市场中创建的所有 SQL Server VM 已预装 SqlIaaSExtension。 AzureBackupWindowsWorkload 扩展使用 SQLIaaSExtension 自动获取所需的权限。
    * 如果 VM 不是从市场创建的，或者你使用的是 SQL 2008 和 2008 R2，则该 VM 可能未安装 SqlIaaSExtension，并且发现操作将会失败并显示错误消息 UserErrorSQLNoSysAdminMembership。 要解决此问题，请按照[设置 VM 权限](backup-azure-sql-database.md#set-vm-permissions)下的说明进行操作。

        ![选择 VM 和数据库](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>配置备份  

1. 在“备份目标” > “步骤2：**配置备份”** 中，选择“配置备份”。

   ![选择“配置备份”](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. 在“选择要备份的项”中，可以看到所有已注册的可用性组和独立的 SQL Server 实例。 选择行左侧的箭头可展开该实例或 AlwaysOn 可用性组中所有未受保护的数据库列表。  

    ![显示包含独立数据库的所有 SQL Server 实例](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. 选择要保护的所有数据库，然后选择“确定”。

   ![保护数据库](./media/backup-azure-sql-database/select-database-to-protect.png)

   为了优化备份负载，Azure 备份会将一个备份作业中的最大数据库数目设置为 50。

     * 若要对 50 个以上的数据库提供保护，请配置多个备份。
     * 若要[启用](#enable-auto-protection)整个实例或 AlwaysOn 可用性组，请在“自动保护”下拉列表中，选择“启用”，然后选择“确定”。

    > [!NOTE]
    > [自动保护](#enable-auto-protection)功能不仅可以一次性针对所有现有数据库启用保护，而且还会自动保护添加到该实例或可用性组的任何新数据库。  

4. 选择“确定”以打开“备份策略”。

    ![为 AlwaysOn 可用性组启用自动保护](./media/backup-azure-sql-database/enable-auto-protection.png)

5. 在“备份策略”中，选择一个策略，然后选择“确定”。

   * 选择默认策略“HourlyLogBackup”。
   * 选择前面为 SQL 创建的现有备份策略。
   * 根据 RPO 和保留范围定义新策略。

     ![选择“备份策略”](./media/backup-azure-sql-database/select-backup-policy.png)

6. 在“备份”中，选择“启用备份”。 

    ![启用选定的备份策略](./media/backup-azure-sql-database/enable-backup-button.png)

7. 在门户的“通知”区域跟踪配置进度。

    ![通知区域](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>创建备份策略

备份策略定义备份创建时间以及这些备份的保留时间。

* 策略是在保管库级别创建的。
* 多个保管库可以使用相同的备份策略，但必须向每个保管库应用该备份策略。
* 创建备份策略时，每日完整备份是默认设置。
* 可以添加差异备份，但仅在将完整备份配置为每周发生时，才能这样做。
* 了解[不同类型的备份策略](backup-architecture.md#sql-server-backup-types)。

创建备份策略：

1. 在保管库中，选择“备份策略” > “添加”。
2. 在“添加”中，选择“Azure VM 中的 SQL Server”以定义策略类型。

   ![为新的备份策略选择策略类型](./media/backup-azure-sql-database/policy-type-details.png)

3. 在“策略名称”处输入新策略的名称。
4. 在“完整备份策略”中选择“备份频率” 。 选择“每日”或“每周”。

   * 如果选择“每日”，请选择备份作业开始时的小时和时区。
   * 如果选择“每周”，请选择备份作业开始时的星期、小时和时区。
   * 运行完整备份；因为不能禁用“完整备份”选项。
   * 选择“完整备份”以查看策略。
   * 对于每日完整备份，无法创建差异备份。

     ![新备份策略字段](./media/backup-azure-sql-database/full-backup-policy.png)  

5. 在“保留范围”中，默认选择所有选项。 清除你不需要的所有保留范围限制，然后设置要使用的间隔。

    * 任何类型备份（完整、差异和日志）的最短保持期均为七天。
    * 恢复点已根据其保留范围标记为保留。 例如，如果选择每日完整备份，则每天只触发一次完整备份。
    * 根据每周保留范围和每周保留设置，将会标记并保留特定日期的备份。
    * 每月和每年保留范围的行为类似。

       ![保留范围间隔设置](./media/backup-azure-sql-database/retention-range-interval.png)

6. 在“完整备份策略”菜单中，选择“确定”接受设置。 
7. 若要添加差异备份策略，请选择“差异备份”。

   ![保留范围间隔设置](./media/backup-azure-sql-database/retention-range-interval.png)
   ![打开差异备份策略菜单](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. 在“差异备份策略”中，选择“启用”打开频率和保留控件。 

    * 每天只能触发一次差异备份。
    * 差异备份最多可以保留 180 天。 对于更长的保留期，请使用完整备份。

9. 选择“确定”保存策略，并返回“备份策略”主菜单。 

10. 若要添加事务日志备份策略，请选择“日志备份”。
11. 在“日志备份”中选择“启用”，并设置频率和保留控件。  日志备份最多可以每隔 15 分钟发生一次，最多可以保留 35 天。
12. 选择“确定”保存策略，并返回“备份策略”主菜单。 

    ![编辑日志备份策略](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. 在“备份策略”菜单中，选择是否启用“SQL 备份压缩”。 默认情况下该选项处于禁用状态。 如果启用，SQL Server 会将压缩备份流发送到 VDI。  请注意，Azure 备份会根据此控件的值，用 COMPRESSION / NO_COMPRESSION 子句覆盖实例级别的默认值。

14. 完成备份策略的编辑后，选择“确定”。

> [!NOTE]
> 每个日志备份都链接到上一个完整备份，以形成恢复链。 此完整备份将保留，直到最后一个日志备份的保留期结束。 这可能意味着完整备份会保留一段额外的时间，以确保所有日志都可以恢复。 假设用户具有每周完整备份、每日差异和 2 小时日志。 所有这些备份都将保留 30 天。 不过，只有在下一个完整备份可用后（即 30 + 7 天后），才会真正清除/删除每周完整备份。 例如，每周完整备份在 11 月 16 日执行。 根据保留策略，它应保留到 12 月 16 日。 此完整备份的最后一次日志备份在下一次计划完整备份（11 月 22 日）之前发生。 在此日志有效期在 12 月 22 日截止之前，不能删除 11 月 16 日的完整备份。 因此，11 月 16 日的完整备份将保留到 12 月 22 日。

## <a name="enable-auto-protection"></a>启用自动保护  

可以启用自动保护，以自动将所有现有和未来数据库备份到独立的 SQL Server 实例或 AlwaysOn 可用性组。

* 可以一次性选择进行自动保护的数据库数目没有限制。
* 启用自动保护时，无法在实例中选择性地保护数据库或排除数据库。
* 如果实例已经包含某些受保护的数据库，即使你启用了自动保护，它们将继续受各自策略的保护。 稍后添加的所有不受保护的数据库将只有在启用自动保护时定义的单个策略，在“配置备份”下列出。 不过，以后可以更改与自动保护的数据库相关联的策略。  

若要启用自动保护：

  1. 在“要备份的项”中，选择要为其启用自动保护的实例。
  2. 选择“自动保护”下的下拉列表，选择“启用”，然后选择“确定”。

      ![针对可用性组启用自动保护](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. 备份是针对所有数据库统一配置的，可以在“备份作业”中跟踪备份。

如果需要禁用自动保护，请选择“配置备份”下面的实例名称，然后针对该实例选择“禁用自动保护”。 所有数据库将继续得到备份，但将来的数据库将不会自动受到保护。

![针对该实例禁用自动保护](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>后续步骤

了解如何操作：

* [还原已备份的 SQL Server 数据库](restore-sql-database-azure-vm.md)
* [管理已备份的 SQL Server 数据库](manage-monitor-sql-database-backup.md)

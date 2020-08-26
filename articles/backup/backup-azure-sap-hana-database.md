---
title: 使用 Azure 备份将 SAP HANA 数据库备份到 Azure
description: 本文介绍如何使用 Azure 备份服务将 SAP HANA 数据库备份到 Azure 虚拟机。
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 61a1dae356209126af47837ec7e8940c394b77a5
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827046"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>备份 Azure VM 中的 SAP HANA 数据库

SAP HANA 数据库是关键工作负荷，要求较低的恢复点目标 (RPO) 和长期保留。 可以使用 [Azure 备份](backup-overview.md)来备份在 Azure 虚拟机 (VM) 上运行的 SAP HANA 数据库。

本文展示了如何将在 Azure VM 上运行的 SAP HANA 数据库备份到 Azure 备份恢复服务保管库。

本文将指导如何进行以下操作：
> [!div class="checklist"]
>
> * 创建并配置保管库
> * 发现数据库
> * 配置备份
> * 运行按需备份作业

>[!NOTE]
>自 2020 年 8 月 1 日起，适用于 RHEL 的 SAP HANA 备份（7.4、7.6、7.7 和 8.1）已正式发布。

>[!NOTE]
>**针对 Azure VM 中 SQL 服务器的软删除以及针对 Azure VM 工作负荷中 SAP HANA 的软删除**现已推出预览版。<br>
>若要注册预览版，请向我们写信 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) 。

## <a name="prerequisites"></a>先决条件

若要为备份注册数据库，请参阅[先决条件](tutorial-backup-sap-hana-db.md#prerequisites)和[预注册脚本的功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)部分。

### <a name="establish-network-connectivity"></a>建立网络连接

对于所有操作，在 Azure VM 上运行的 SAP HANA 数据库需要连接到 Azure 备份服务、Azure 存储和 Azure Active Directory。 这可以通过使用专用终结点，或允许访问所需的公共 IP 地址或 FQDN 来实现。 如果不允许正确连接到所需的 Azure 服务，则可能会导致诸如数据库发现、配置备份、执行备份和还原数据等操作失败。

下表列出了可用于建立连接的各种备选方案：

| **选项**                        | **优点**                                               | **缺点**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 专用终结点                 | 允许通过虚拟网络中的专用 IP 进行备份  <br><br>   提供网络和保管库端的精细控制 | 产生标准专用终结点[开销](https://azure.microsoft.com/pricing/details/private-link/) |
| NSG 服务标记                  | 由于范围更改会自动合并，因此管理变得更容易   <br><br>   无额外成本 | 只可用于 NSG  <br><br>    提供对整个服务的访问 |
| Azure 防火墙 FQDN 标记          | 自动管理必需的 FQDN，因此更易于管理 | 只可用于 Azure 防火墙                         |
| 允许访问服务 FQDN/IP | 无额外成本   <br><br>  适用于所有网络安全设备和防火墙 | 可能需要访问一组广泛的 IP 或 FQDN   |
| 使用 HTTP 代理                 | 对 VM 进行单点 Internet 访问                       | 通过代理软件运行 VM 带来的额外成本         |

关于使用这些选项的更多细节如下：

#### <a name="private-endpoints"></a>专用终结点

使用专用终结点，可以从虚拟网络内的服务器安全地连接到恢复服务保管库。 专用终结点为保管库使用 VNET 地址空间中的 IP。 虚拟网络中的资源与保管库之间的网络流量将通过虚拟网络和 Microsoft 主干网络上的专用链接传输。 这样就不会从公共 Internet 泄露信息。 在[此处](./private-endpoints.md)详细了解 Azure 备份的专用终结点。

#### <a name="nsg-tags"></a>NSG 标记

如果使用网络安全组 (NSG)，请使用 AzureBackup 服务标记以允许对 Azure 备份进行出站访问。 除了 Azure 备份标记外，还需要通过为 Azure AD 和 Azure 存储创建类似的 [NSG 规则](../virtual-network/security-overview.md#service-tags)，以便在连接后进行身份验证和数据传输。  以下步骤介绍了为 Azure 备份标记创建规则的过程：

1. 在“所有服务”中转到“网络安全组”，然后选择“网络安全组”。

1. 在“设置”下选择“出站安全规则”。

1. 选择 **添加** 。 根据[安全规则设置](../virtual-network/manage-network-security-group.md#security-rule-settings)中所述，输入创建新规则所需的所有详细信息。 请确保将选项“目标”设置为“服务标记”，将“目标服务标记”设置为“AzureBackup”。

1. 单击“添加”，保存新创建的出站安全规则。

同样，可以为 Azure 存储和 Azure AD 创建 NSG 出站安全规则。 有关服务标记的详细信息，请参阅[此文](../virtual-network/service-tags-overview.md)。

#### <a name="azure-firewall-tags"></a>Azure 防火墙标记

如果使用 Azure 防火墙，请使用 *AzureBackup* [Azure 防火墙 FQDN 标记](../firewall/fqdn-tags.md)创建应用程序规则。 这允许对 Azure 备份进行所有出站访问。

#### <a name="allow-access-to-service-ip-ranges"></a>允许访问服务 IP 范围

如果选择允许访问服务 IP，请参阅[此处](https://www.microsoft.com/download/confirmation.aspx?id=56519)的 JSON 文件中的 IP 范围。 你需要允许访问与 Azure 备份、Azure 存储和 Azure Active Directory 对应的 IP。

#### <a name="allow-access-to-service-fqdns"></a>允许访问服务 FQDN

还可以使用以下 FQDN 以允许从服务器访问所需的服务：

| 服务    | 要访问的域名                             |
| -------------- | ------------------------------------------------------------ |
| Azure 备份  | `*.backup.windowsazure.com`                             |
| Azure 存储 | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | 根据[这篇文章](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)，允许访问第 56 和 59 节下的 FQDN |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>使用 HTTP 代理服务器路由流量

备份在 Azure VM 上运行的 SAP HANA 数据库时，该 VM 上的备份扩展将使用 HTTPS API 将管理命令发送到 Azure 备份，并将数据发送到 Azure 存储。 备份扩展还使用 Azure AD 进行身份验证。 通过 HTTP 代理路由这三个服务的备份扩展流量。 使用上面提到的 IP 和 FQDN 列表，以允许访问所需的服务。 不支持已经过身份验证的代理服务器。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>发现数据库

1. 在保管库中，单击“开始使用”中的“备份” 。 在“工作负荷在哪里运行?”中，选择“Azure VM 中的 SAP HANA” 。
2. 单击“启动发现”。 这会开始在保管库区域中发现未受保护的 Linux VM。

   * 在发现后，未受保护的 VM 将显示在门户中，按名称和资源组列出。
   * 如果某个 VM 未按预期列出，请检查它是否已在保管库中备份。
   * 可能有多个 VM 同名，但属于不同的资源组。

3. 在“选择虚拟机”中，单击脚本下载链接。此脚本可为 Azure 备份服务提供访问 SAP HANA VM 的权限以进行数据库发现。
4. 在托管要备份的 SAP HANA 数据库的每个 VM 上运行此脚本。
5. 在 VM 上运行此脚本后，在“选择虚拟机”中选择 VM。 然后单击“发现数据库”。
6. Azure 备份可发现该 VM 上的所有 SAP HANA 数据库。 在发现期间，Azure Backup 将 VM 注册到保管库，并在该 VM 上安装扩展。 不会在数据库中安装任何代理。

    ![发现 SAP HANA 数据库](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>配置备份  

现在启用备份。

1. 在步骤 2 中，单击“配置备份”。

    ![配置备份](./media/backup-azure-sap-hana-database/configure-backup.png)
2. 在“选择要备份的项”中，选择要保护的所有数据库，然后选择“确定”。

    ![选择要备份的项](./media/backup-azure-sap-hana-database/select-items.png)
3. 在“备份策略” > “选择备份策略”中，按照下面的说明，为数据库创建一个新的备份策略。 

    ![选择备份策略](./media/backup-azure-sap-hana-database/backup-policy.png)
4. 创建策略后，在“备份”菜单中单击“启用备份” 。

    ![启用备份](./media/backup-azure-sap-hana-database/enable-backup.png)
5. 在门户的“通知”区域跟踪备份配置进度。

### <a name="create-a-backup-policy"></a>创建备份策略

备份策略定义备份创建时间以及这些备份的保留时间。

* 策略是在保管库级别创建的。
* 多个保管库可以使用相同的备份策略，但必须向每个保管库应用该备份策略。

>[!NOTE]
>备份在 Azure VM 中运行的 SAP HANA 数据库时，Azure 备份不会针对夏令时更改自动进行调整。
>
>请根据需要手动修改策略。

按以下方式指定策略设置：

1. 在“策略名称”处输入新策略的名称。

   ![输入策略名称](./media/backup-azure-sap-hana-database/policy-name.png)
2. 在“完整备份策略”中选择“备份频率”，然后选择“每日”或“每周”。   
   * **每日**：选择开始备份作业的小时和时区。
       * 你必须运行完整备份。 无法关闭此选项。
       * 单击“完整备份”以查看策略。
       * 对于每日完整备份，无法创建差异备份。
   * **每周**：选择运行备份作业的星期、小时和时区。

   ![选择备份频率](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. 在“保持期”中，对完整备份配置保留设置。
    * 默认情况下将选择所有选项。 清除你不想使用的所有保持期限制，并设置要使用的选项。
    * 任何备份类型（完整/差异/日志）的最短保持期均为七天。
    * 恢复点已根据其保留范围标记为保留。 例如，如果选择每日完整备份，则每天只触发一次完整备份。
    * 根据每周保持期和设置，将会标记并保留特定日期的备份。
    * 每月和每年保留范围的行为类似。

4. 在“完整备份策略”菜单中，单击“确定”接受设置 。
5. 选择“差异备份”，以添加差异策略。
6. 在“差异备份策略”中，选择“启用”打开频率和保留控件。 
    * 每天最多可以触发一次差异备份。
    * 差异备份最多可以保留 180 天。 如果需要保留更长时间，必须使用完整备份。

    ![差异备份策略](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > 目前不支持增量备份。

7. 单击“确定”保存策略，并返回“备份策略”主菜单 。
8. 请选择“日志备份”，以添加事务日志备份策略。
    * 在“日志备份”中，选择“启用”。   由于 SAP HANA 管理所有日志备份，因此无法禁用此功能。
    * 设置频率和保留期控制。

    > [!NOTE]
    > 日志备份仅在成功完成一次完整备份之后进行。

9. 单击“确定”保存策略，并返回“备份策略”主菜单 。
10. 完成定义备份策略后，单击“确定”。

> [!NOTE]
> 每个日志备份都链接到上一个完整备份，以形成恢复链。 此完整备份将一直保留到最后一个日志备份的保留期结束为止。 这可能意味着完整备份会保留一段额外的时间，以确保所有日志都可以恢复。 假设用户有每周完整备份、每日差异备份和 2 小时日志备份。 所有这些备份都将保留 30 天。 但是，只有在下一次完整备份可用之后（即30到7天后），才能真正清除/删除每周的完全备份。 对于示例，每周一次完整备份在11月16日执行。 根据保留策略，它应保留到 12 月 16 日。 该完整备份的最后一次日志备份发生在下一次计划的完整备份之前，即 11 月 22 日。 必须等到 12 月 22 日此日志备份可用后，才能删除 11 月 16 日的完整备份。 因此，11 月 16 日的完整备份将保留到 12 月 22 日。

## <a name="run-an-on-demand-backup"></a>运行按需备份

备份按照策略计划运行。 可以如下所述运行按需备份：

1. 在保管库菜单中，单击“备份项”。
2. 在“备份项”中，选择运行 SAP HANA 数据库的 VM，然后单击“立即备份”。
3. 在“立即备份”中，选择要执行的备份的类型。 。 此备份将根据与此备份项关联的策略进行保留。
4. 监视门户通知。 可以在保管库仪表板 >“备份作业” > “进行中”监视作业进度。  创建初始备份可能需要一些时间，具体取决于你的数据库的大小。

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>在启用了 Azure 备份的数据库上运行 SAP HANA Studio 备份

如果要使用 Azure 备份创建正在备份的数据库的本地备份（使用 HANA Studio），请执行以下操作：

1. 等待数据库的所有完整备份或日志备份完成。 在 SAP HANA Studio / Cockpit 中检查状态。
1. 禁用日志备份，并将备份目录设置为相关数据库的文件系统。
1. 为此，请双击“systemdb” > “配置” > “选择数据库” > “筛选器(日志)”。   
1. 将 **enable_auto_log_backup** 设置为 **No**。
1. 将 **log_backup_using_backint** 设置为 **False**。
1. 将 **catalog_backup_using_backint** 设置为 **False**。
1. 创建数据库的完整备份。
1. 等待完整备份和目录备份完成。
1. 将前面的设置恢复为 Azure 的设置：
    * 将 **enable_auto_log_backup** 设置为 **Yes**。
    * 将 **log_backup_using_backint** 设置为 **True**。
    * 将 **catalog_backup_using_backint** 设置为 **True**。

## <a name="next-steps"></a>后续步骤

* 了解如何[还原在 Azure VM 上运行的 SAP HANA 数据库](./sap-hana-db-restore.md)
* 了解如何[管理使用 Azure 备份进行备份的 SAP HANA 数据库](./sap-hana-db-manage.md)

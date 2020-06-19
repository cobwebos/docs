---
title: 排查 SAP HANA 数据库备份错误
description: 介绍如何排查在使用 Azure 备份来备份 SAP HANA 数据库时可能遇到的常见错误。
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 5c1ad55a86e80808b9055fd1b34a2d72209464a2
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697062"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>排查 Azure 上 SAP HANA 数据库的备份问题

本文提供了有关在 Azure 虚拟机上备份 SAP HANA 数据库的疑难解答信息。 要详细了解当前支持的 SAP HANA 备份方案，请参阅[方案支持](sap-hana-backup-support-matrix.md#scenario-support)。

## <a name="prerequisites-and-permissions"></a>先决条件和权限

在配置备份之前，请参考[先决条件](tutorial-backup-sap-hana-db.md#prerequisites)和[预注册脚本的功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)部分。

## <a name="common-user-errors"></a>常见用户错误

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **错误消息**      | <span style="font-weight:normal">Azure 备份没有执行备份所需的角色权限</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **可能的原因**    | 该角色可能已被覆盖。                          |
| **建议的操作** | 若要解决此问题，请从“发现 DB”窗格运行该脚本，或点击[此处](https://aka.ms/scriptforpermsonhana)下载该脚本。 或者，将“SAP_INTERNAL_HANA_SUPPORT”角色添加到工作负载备份用户 (AZUREWLBACKUPHANAUSER)。 |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| 错误消息      | <span style="font-weight:normal">未能连接到 HANA 系统</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | SAP HANA 实例可能已关闭。<br/>未设置 Azure 备份与 HANA 数据库进行交互所需的权限。 |
| **建议的操作** | 检查 SAP HANA 数据库是否已启动。 如果数据库已启动并正在运行，请检查是否设置了所有必需的权限。 如果缺少任何权限，请运行 [预注册脚本](https://aka.ms/scriptforpermsonhana)以添加缺少的权限。 |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| 错误消息      | <span style="font-weight:normal">指定的 SAP HANA 实例无效或无法找到</span>  |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 无法备份一个 Azure VM 上的多个 SAP HANA 实例。 |
| **建议的操作** | 在要备份的 SAP HANA 实例上运行[预注册脚本](https://aka.ms/scriptforpermsonhana)。 如果问题仍然存在，请联系 Microsoft 支持部门。 |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| 错误消息      | <span style="font-weight:normal">指定的 SAP HANA 操作不受支持</span>              |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 适用于 SAP HANA 的 Azure 备份不支持对 SAP HANA 本机客户端 (Studio/Cockpit/DBA Cockpit) 执行增量备份和操作 |
| **建议的操作** | 有关详细信息，请参阅[此处](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)。 |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| 错误消息      | <span style="font-weight:normal">此 SAP HANA 数据库不支持请求的备份类型</span>  |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | Azure 备份不支持增量备份和使用快照进行的备份 |
| **建议的操作** | 有关详细信息，请参阅[此处](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)。 |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| 错误消息      | <span style="font-weight:normal">备份日志链中断</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 日志备份目标可能已从 backint 更新为文件系统，也可能是 backint 可执行文件发生了更改 |
| **建议的操作** | 请触发完整备份以解决此问题                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| 错误消息      | <span style="font-weight:normal">检测到 SDC 到 MDC 的升级</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | SAP HANA 实例已从 SDC 升级到 MDC。 更新后，备份会失败。 |
| **建议的操作** | 按照[从 SDC 升级到 MDC](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#sdc-to-mdc-upgrade-with-a-change-in-sid) 中列出的步骤解决此问题 |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| 错误消息      | <span style="font-weight:normal">检测到无效的 backint 配置</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 未正确为 Azure 备份指定备份参数 |
| **建议的操作** | 检查是否设置了以下 (backint) 参数：<br/>\* [catalog_backup_using_backint:true]<br/>\* [enable_accumulated_catalog_backup:false]<br/>\* [parallel_data_backup_backint_channels:1]<br/>\* [log_backup_timeout_s：900）]<br/>\* [backint_response_timeout:7200]<br/>如果主机中存在基于 backint 的参数，请将其删除。 如果主机级别不存在参数，但在数据库级别手动修改了参数，请将它们还原为前面所述的相应值。 或者，从 Azure 门户运行[停止保护并保留备份数据](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database)，然后选择“恢复备份”。 |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|错误消息  |还原的源系统和目标系统不兼容  |
|---------|---------|
|可能的原因   | 为还原选择的源系统和目标系统不兼容        |
|建议的操作   |   确保还原方案不属于以下可能的不兼容还原： <br><br>   **案例 1：** 在还原过程中无法重命名 SYSTEMDB。  <br><br> **案例 2：** 源 - SDC 和目标 - MDC：源数据库无法还原为目标 SYSTEMDB 或租户 DB。 <br><br> **案例 3：** 源 - MDC 和目标 - SDC：源数据库（SYSTEMDB 或租户 DB）无法还原到目标。 <br><br>  有关详细信息，请参阅 [SAP 支持启动板](https://launchpad.support.sap.com)中的备注 1642148。 |

## <a name="restore-checks"></a>还原检查

### <a name="single-container-database-sdc-restore"></a>单容器数据库 (SDC) 还原

在将 HANA 的单容器数据库 (SDC) 还原到另一个 SDC 计算机时，注意提供正确的输入。 数据库名称应采用小写形式，后跟用英文括号括起的“sdc”。 HANA 实例将以大写字母显示。

假设已备份 SDC HANA 实例“H21”。 “备份项”页会将备份项名称显示为“h21(sdc)”。 如果尝试将此数据库还原到另一个目标 SDC（如 H11），则需要提供以下输入。

![还原的 SDC 数据库名称](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

请注意以下几点：

- 默认情况下，还原的数据库名称将用备份项名称填充。 在本例中为 h21(sdc)。
- 选择“H11”作为目标不会自动更改还原的数据库名称。 应将其编辑为 h11(sdc)。 对于 SDC，还原的数据库名称将是小写字母后跟用英文括号括起的“sdc”的目标实例 ID。
- 由于 SDC 只能有一个数据库，因此还需要单击该复选框，允许使用恢复点数据替代现有的数据库数据。
- Linux 区分大小写。 因此，请注意保留原来的大小写。

### <a name="multiple-container-database-mdc-restore"></a>多容器数据库 (MDC) 还原

在 HANA 的多容器数据库中，标准配置为 SYSTEMDB + 至少 1 个租户 DB。 还原整个 SAP HANA 实例意味着还原 SYSTEMDB 和租户 DB。 首先还原 SYSTEMDB，接着还原租户 DB。 系统 DB 实质上意味着替代所选目标上的系统信息。 此还原还会替代目标实例中与 BackInt 相关的信息。 因此，在将系统 DB 还原到目标实例之后，请再次运行预注册脚本。 只有这样，随后的租户 DB 还原才会成功。

## <a name="back-up-a-replicated-vm"></a>备份复制的 VM

### <a name="scenario-1"></a>方案 1

使用 Azure Site Recovery 或 Azure VM 备份复制原始 VM。 新 VM 是为模拟旧的 VM 而构建的。 也就是说，设置完全相同。 （这是因为原始 VM 已被删除，并且已通过 VM 备份或 Azure Site Recovery 完成了还原）。

此方案可包含两种可能的情况。 了解如何在这两种情况下备份复制的 VM：

1. 创建的新 VM 与已删除的 VM 同名，并且位于已删除的 VM 所属的资源组和订阅中。

    - 此扩展已在 VM 中，但对所有服务都不可见
    - 运行预注册脚本
    - 在 Azure 门户中为同一计算机重新注册扩展（“备份” -> “查看详细信息”->“选择相关 Azure VM”->“重新注册”） 
    - 然后，现有的已备份数据库（源自已删除的 VM）应该会成功开始备份

2. 创建的新 VM 会出现以下两种情况之一：

    - 名称与已删除的 VM 不同
    - 名称与已删除的 VM 相同，但资源组或订阅不同于已删除的 VM

    如果出现上述情况，请执行以下步骤：

    - 此扩展已在 VM 中，但对所有服务都不可见
    - 运行预注册脚本
    - 如果发现并保护新数据库，你将在门户中看到重复的活动数据库。 为避免出现这种情况，请对旧数据库[停止保护并保留数据](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)。 然后继续执行其余步骤。
    - 发现数据库以启用备份
    - 对这些数据库启用备份
    - 现有的已备份数据库（源自已删除的 VM）将继续存储在保管库中（根据相应策略保留其备份）

### <a name="scenario-2"></a>方案 2

使用 Azure Site Recovery 或 Azure VM 备份复制原始 VM。 新 VM 是用模板内容构建的。 这是一个新的 VM，具有新的 SID。

请按照以下步骤对新 VM 启用备份：

- 此扩展已在 VM 上，但对所有服务都不可见
- 运行预注册脚本。 根据新 VM 的 SID，可能会出现两种情况：
  - 原始 VM 和新 VM 具有相同的 SID。 预注册脚本将成功运行。
  - 原始 VM 和新 VM 具有不同的 SID。 预注册脚本将失败。 请联系支持人员以获取此方案的帮助。
- 发现要备份的数据库
- 对这些数据库启用备份

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>在同一 VM 上升级 SDC 版本或 MDC 版本

要升级 OS、更改 SDC 版本或更改 MDC 版本，但不会导致 SID 更改，可以按以下方式处理：

- 确保新的 OS 版本、SDC 或 MDC 版本当前[受 Azure 备份支持](sap-hana-backup-support-matrix.md#scenario-support)
- 对数据库[停止保护并保留数据](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)
- 执行升级或更新
- 重新运行预注册脚本。 通常，升级过程会删除必需的角色。 运行预注册脚本有助于验证所有必需的角色
- 再次恢复对数据库的保护

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>从 SDC 升级到 MDC（不更改 SID）

要从 SDC 升级到 MDC 而不导致 SID 更改，可以按如下方式进行处理：

- 确保新的 MDC 版本当前[受 Azure 备份支持](sap-hana-backup-support-matrix.md#scenario-support)
- 对旧的 SDC 数据库[停止保护并保留数据](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)
- 执行升级。 完成后，HANA 系统现在便是包含一个系统 DB 和多个租户 DB 的 MDC
- 重新运行[预注册脚本](https://aka.ms/scriptforpermsonhana)
- 在 Azure 门户中为同一计算机重新注册扩展（“备份” -> “查看详细信息”->“选择相关 Azure VM”->“重新注册”） 
- 针对同一 VM 单击“重新发现 DB”。 此操作应将步骤 3 中的新 DB 显示为 SYSTEMDB 和租户 DB，而不是 SDC
- 旧的 SDC 数据库将继续位于保管库中，并根据相应策略保留旧的备份数据
- 为这些数据库配置备份

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>从 SDC 升级到 MDC（更改 SID）

要从 SDC 升级到 MDC 并更改 SID，可以按如下方式进行处理：

- 确保新的 MDC 版本当前[受 Azure 备份支持](sap-hana-backup-support-matrix.md#scenario-support)
- 对旧的 SDC 数据库**停止保护并保留数据**
- 执行升级。 完成后，HANA 系统现在便是包含一个系统 DB 和多个租户 DB 的 MDC
- 重新运行具有正确详细信息（新 SID 和 MDC）的[预注册脚本](https://aka.ms/scriptforpermsonhana)。 由于 SID 发生了更改，你可能会遇到阻碍脚本成功运行的问题。 如果遇到问题，请联系 Azure 备份支持部门。
- 在 Azure 门户中为同一计算机重新注册扩展（“备份” -> “查看详细信息”->“选择相关 Azure VM”->“重新注册”） 
- 针对同一 VM 单击“重新发现 DB”。 此操作应将步骤 3 中的新 DB 显示为 SYSTEMDB 和租户 DB，而不是 SDC
- 旧的 SDC 数据库将继续位于保管库中，并根据相应策略保留旧的备份数据
- 为这些数据库配置备份

## <a name="re-registration-failures"></a>重新注册失败

在触发重新注册操作之前，请检查是否存在以下一种或多种症状：

- VM 上所有操作（例如备份、还原和配置备份）都失败，并出现以下某一错误代码：WorkloadExtensionNotReachable、UserErrorWorkloadExtensionNotInstalled、WorkloadExtensionNotPresent、WorkloadExtensionDidntDequeueMsg。
- 如果备份项的“备份状态”区域显示“无法访问”，请排除可能导致相同状态的所有其他原因 ：

  - 缺少对 VM 执行与备份相关的操作的权限
  - VM 已关闭，因此无法进行备份
  - 网络问题

由于下面的一个或多个原因，可能会出现这些症状：

- 扩展从门户中删除或卸载。
- VM 通过就地磁盘还原及时还原。
- VM 关闭的时间过长，因此其扩展配置已过期。
- VM 被删除，并且在已删除 VM 所在的资源组中创建了另一个具有相同名称的 VM。

在上述方案中，我们建议你对 VM 触发重新注册操作。

## <a name="next-steps"></a>后续步骤

- 请查看有关如何在 Azure VM 上备份 SAP HANA 数据库的[常见问题解答](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm)。

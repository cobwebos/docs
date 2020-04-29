---
title: 排查 SAP HANA 数据库备份错误
description: 介绍如何排查在使用 Azure 备份对 SAP HANA 数据库进行备份时可能发生的常见错误。
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 6520f106011b632da2725f456aeb278c7748ddc9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79459304"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>排查 Azure 上的 SAP HANA 数据库备份问题

本文针对在 Azure 虚拟机上备份 SAP HANA 数据库时出现的问题提供故障排除信息。 有关目前支持的 SAP HANA 备份方案的详细信息，请参阅[方案支持](sap-hana-backup-support-matrix.md#scenario-support)。

## <a name="prerequisites-and-permissions"></a>先决条件和权限

在继续备份之前，请参阅[先决条件](tutorial-backup-sap-hana-db.md#prerequisites)和[注册前脚本的功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)部分。

## <a name="common-user-errors"></a>常见用户错误

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **错误消息**      | <span style="font-weight:normal">Azure 备份没有所需的角色权限，无法执行备份</span>    |
| ---------------------- | ------------------------------------------------------------ |
| 可能的原因     | 角色可能已被覆盖。                          |
| **建议的操作** | 若要解决该问题，请运行“发现数据库”窗格中的脚本，或在[此处](https://aka.ms/scriptforpermsonhana)下载该脚本。  或者，将“SAP_INTERNAL_HANA_SUPPORT”角色添加到工作负荷备份用户 (AZUREWLBACKUPHANAUSER)。 |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| 错误消息      | <span style="font-weight:normal">未能连接到 HANA 系统</span>                        |
| ------------------ | ------------------------------------------------------------ |
| 可能的原因     | SAP HANA 实例可能已关闭。<br/>未设置 Azure 备份在与 HANA 数据库交互时所需的权限。 |
| **建议的操作** | 检查 SAP HANA 数据库是否已启动。 如果数据库已启动并在运行，请检查是否设置了所有所需权限。 如果缺少任何权限，请运行[注册前脚本](https://aka.ms/scriptforpermsonhana)添加缺少的权限。 |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| 错误消息      | <span style="font-weight:normal">指定的 SAP HANA 实例无效或无法找到</span>  |
| ------------------ | ------------------------------------------------------------ |
| 可能的原因     | 无法备份单个 Azure VM 上的多个 SAP HANA 实例。 |
| **建议的操作** | 在要备份的 SAP HANA 实例上运行[注册前脚本](https://aka.ms/scriptforpermsonhana)。 如果问题依然出现，请联系 Microsoft 支持人员。 |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| 错误消息      | <span style="font-weight:normal">指定的 SAP HANA 操作不受支持</span>              |
| ------------------ | ------------------------------------------------------------ |
| 可能的原因     | 适用于 SAP HANA 的 Azure 备份不支持在 SAP HANA 本机客户端 (Studio/Cockpit/DBA Cockpit) 上执行的增量备份和操作 |
| **建议的操作** | 有关详细信息，请参阅[此文](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)。 |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| 错误消息      | <span style="font-weight:normal">此 SAP HANA 数据库不支持请求的备份类型</span>  |
| ------------------ | ------------------------------------------------------------ |
| 可能的原因     | Azure 备份不支持增量备份和使用快照的备份 |
| **建议的操作** | 有关详细信息，请参阅[此文](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)。 |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| 错误消息      | <span style="font-weight:normal">备份日志链中断</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| 可能的原因     | 日志备份目标可能已从 backint 更新为文件系统，或者 backint 可执行文件可能已更改 |
| **建议的操作** | 触发完整备份即可解决该问题                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSystsemsForRestore

| 错误消息      | <span style="font-weight:normal">还原操作的源系统和目标系统不兼容</span>    |
| ------------------ | ------------------------------------------------------------ |
| 可能的原因     | 还原操作的目标系统与源系统不兼容 |
| **建议的操作** | 请参阅 SAP 说明 [1642148](https://launchpad.support.sap.com/#/notes/1642148) 了解目前支持的还原类型 |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| 错误消息      | <span style="font-weight:normal">检测到 SDC 到 MDC 的升级</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| 可能的原因     | SAP HANA 实例已从 SDC 升级到 MDC。 更新后，备份将会失败。 |
| **建议的操作** | 遵循[从 SAP HANA 1.0 升级到 2.0](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) 部分中所列的步骤来解决该问题 |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| 错误消息      | <span style="font-weight:normal">检测到无效的 backint 配置</span>                       |
| ------------------ | ------------------------------------------------------------ |
| 可能的原因     | 为 Azure 备份指定了错误的后备参数 |
| **建议的操作** | 检查是否设置了以下 (backint) 参数：<br/>\* [catalog_backup_using_backint:true]<br/>\* [enable_accumulated_catalog_backup:false]<br/>\* [parallel_data_backup_backint_channels:1]<br/>\* [log_backup_timeout_s:900)]<br/>\* [backint_response_timeout:7200]<br/>如果 HOST 中存在基于 backint 的参数，请删除这些参数。 如果在 HOST 级别不存在参数，但在数据库级别手动修改了相应参数，请根据前面所述将其还原为适当的值。 或者，在 Azure 门户中运行[停止保护并保留备份数据](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database)，然后选择“恢复备份”。  |

## <a name="restore-checks"></a>还原检查

### <a name="single-container-database-sdc-restore"></a>单容器数据库 (SDC) 还原

在将 HANA 的单容器数据库 (SDC) 还原到另一台 SDC 计算机时请小心提供输入。 数据库名称应以小写字母提供，并在括号中追加“sdc”。 HANA 实例将以大写字母显示。

假设备份了 SDC HANA 实例“H21”。 备份项页会将备份项名称显示为“h21(sdc)”。  如果尝试将此数据库还原到另一个目标 SDC（如 H11），则需要提供以下输入。

![还原的 SDC 数据库名称](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

请注意以下几点：

- 默认情况下会使用备份项名称填充还原的数据库名称。 在本例中为 h21(sdc)。
- 选择 H11 作为目标不会自动更改已还原的数据库名称。 **应将其编辑为 h11(sdc)** 。 对于 SDC，还原的数据库名称将是小写字母形式的目标实例 ID，并且在括号中追加了“sdc”。
- 由于 SDC 只能包含一个数据库，因此还需要单击相应的复选框，以允许使用恢复点数据替代现有的数据库数据。
- 在 Linux 中，此项输入区分大小写。 因此请小心保留大小写。

### <a name="multiple-container-database-mdc-restore"></a>多容器数据库 (MDC) 还原

在 HANA 的多容器数据库中，标准配置是 SYSTEMDB 加上一个或多个租户数据库。 还原整个 SAP HANA 实例意味着还原 SYSTEMDB 和租户数据库。 需要先还原 SYSTEMDB，然后继续还原租户数据库。 还原系统数据库实质上旨在替代所选目标上的系统信息。 这种还原还会替代目标实例中与 BackInt 相关的信息。 因此，在将系统数据库还原到目标实例后，请再次运行注册前脚本。 只有这样，随后的租户数据库还原才会成功。

## <a name="upgrading-from-sap-hana-10-to-20"></a>从 SAP HANA 1.0 升级到 2.0

如果你正在保护 SAP HANA 1.0 数据库并希望升级到 2.0，请执行以下步骤：

- [停止保护](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)，并保留旧 SDC 数据库的数据。
- 执行升级。 完成后，现在 HANA 就是包含系统数据库和租户数据库的 MDC
- 重新运行包含正确 sid 和 mdc 详细信息的[注册前脚本](https://aka.ms/scriptforpermsonhana)。
- 在 Azure 门户中重新注册同一计算机的扩展（“备份”->“查看详细信息”->“选择相关的 Azure VM”->“重新注册”）。
- 单击同一 VM 对应的“重新发现数据库”。 此操作应会显示步骤 2 中所述的新数据库及其正确的详细信息（SYSTEMDB 和租户数据库，而不是 SDC）。
- 为这些新数据库配置备份。

## <a name="upgrading-without-an-sid-change"></a>在不更改 SID 的情况下升级

可按下文所述，在不更改 SID 的情况下对 OS 或 SAP HANA 进行升级：

- [停止保护](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)，并保留数据库的数据
- 执行升级。
- 重新运行[注册前脚本](https://aka.ms/scriptforpermsonhana)。 通常，升级过程会删除所需的角色。 运行注册前脚本可帮助验证所有所需角色。
- 再次对数据库[恢复保护](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database)

## <a name="re-registration-failures"></a>重新注册失败

在触发重新注册操作之前，请检查是否存在以下一种或多种症状：

- 所有操作（例如备份、还原和配置备份）在 VM 上失败，并出现以下错误代码之一： **WorkloadExtensionNotReachable、UserErrorWorkloadExtensionNotInstalled、WorkloadExtensionNotPresent、WorkloadExtensionDidntDequeueMsg**。
- 如果备份项的“备份状态”**** 区域显示为“无法访问”****，请排除可能导致相同状态的其他所有原因：

  - 缺少在 VM 上执行备份相关操作的权限
  - VM 已关闭，因此无法进行备份
  - 网络问题

这些症状可能是以下一种或多种原因造成的：

- 从门户中删除或卸载了某个扩展。
- 已通过就地磁盘还原将该 VM 还原到某个时间点。
- 该 VM 已关闭较长时间，因此其上的扩展配置已过期。
- 删除了该 VM，并在该 VM 所在的同一资源组中创建了同名的另一个 VM。

对于上述场景，我们建议在 VM 上触发重新注册操作。

## <a name="next-steps"></a>后续步骤

- 查看有关在 Azure Vm 上备份 SAP HANA 数据库[的常见问题。](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm)

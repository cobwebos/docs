---
title: 排除 SAP HANA 数据库备份错误
description: 介绍如何使用 Azure 备份备份 SAP HANA 数据库时可能出现的常见错误。
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 6520f106011b632da2725f456aeb278c7748ddc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459304"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>在 Azure 上对 SAP HANA 数据库进行故障排除备份

本文提供用于在 Azure 虚拟机上备份 SAP HANA 数据库的故障排除信息。 有关当前支持的 SAP HANA 备份方案的详细信息，请参阅[方案支持](sap-hana-backup-support-matrix.md#scenario-support)。

## <a name="prerequisites-and-permissions"></a>先决条件和权限

在配置备份之前，请参阅[先决条件](tutorial-backup-sap-hana-db.md#prerequisites)和[预注册脚本执行](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)哪些部分。

## <a name="common-user-errors"></a>常见用户错误

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **错误消息**      | <span style="font-weight:normal">Azure 备份没有执行备份所需的角色权限</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **可能的原因**    | 角色可能已被覆盖。                          |
| **建议的操作** | 要解决此问题，请从 **"发现数据库"** 窗格运行脚本，或[在此处](https://aka.ms/scriptforpermsonhana)下载该脚本。 或者，将"SAP_INTERNAL_HANA_SUPPORT"角色添加到工作负载备份用户（AZUREWLBACKUPHANAUSER）。 |

### <a name="usererrorinopeninghanaodbcconnection"></a>用户错误在打开哈纳奥德布连接

| 错误消息      | <span style="font-weight:normal">无法连接到 HANA 系统</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | SAP HANA 实例可能已关闭。<br/>未设置 Azure 备份与 HANA 数据库交互所需的权限。 |
| **建议的操作** | 检查 SAP HANA 数据库是否已启动。 如果数据库已启动并运行，请检查是否设置了所有必需的权限。 如果缺少任何权限，则运行[预注册脚本](https://aka.ms/scriptforpermsonhana)以添加缺少的权限。 |

### <a name="usererrorhanainstancenameinvalid"></a>用户错误"实例名称无效"

| 错误消息      | <span style="font-weight:normal">指定的 SAP HANA 实例无效或找不到</span>  |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 无法备份单个 Azure VM 上的多个 SAP HANA 实例。 |
| **建议的操作** | 在要备份的 SAP HANA 实例上运行[预注册脚本](https://aka.ms/scriptforpermsonhana)。 如果问题仍然存在，请与 Microsoft 支持部门联系。 |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| 错误消息      | <span style="font-weight:normal">不支持指定的 SAP HANA 操作</span>              |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | SAP HANA 的 Azure 备份不支持对 SAP HANA 本机客户端（工作室/驾驶舱/DBA 驾驶舱）执行的增量备份和操作 |
| **建议的操作** | 有关详细信息，请参阅[此处](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)。 |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>用户错误哈纳波多不支持备份类型

| 错误消息      | <span style="font-weight:normal">此 SAP HANA 数据库不支持请求的备份类型</span>  |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | Azure 备份不支持使用快照的增量备份和备份 |
| **建议的操作** | 有关详细信息，请参阅[此处](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)。 |

### <a name="usererrorhanalsnvalidationfailure"></a>用户错误HANALS验证失败

| 错误消息      | <span style="font-weight:normal">备份日志链已断开</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 日志备份目标可能已由回音更新到文件系统，或者返回文件可执行文件可能已更改 |
| **建议的操作** | 触发完整备份以解决此问题                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>用户错误可协调SrcTargetsystsems用于还原

| 错误消息      | <span style="font-weight:normal">用于还原的源系统和目标系统不兼容</span>    |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 用于还原的目标系统与源不兼容 |
| **建议的操作** | 请参阅 SAP 说明[1642148，](https://launchpad.support.sap.com/#/notes/1642148)了解当前支持的还原类型 |

### <a name="usererrorsdctomdcupgradedetected"></a>检测到用户错误DCtoMDC升级

| 错误消息      | <span style="font-weight:normal">检测到 SDC 到 MDC 升级</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | SAP HANA 实例已从 SDC 升级到 MDC。 更新后备份将失败。 |
| **建议的操作** | 按照 SAP [HANA 1.0 到 2.0 部分](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20)中列出的步骤解决问题 |

### <a name="usererrorinvalidbackintconfiguration"></a>用户错误无效的备份配置

| 错误消息      | <span style="font-weight:normal">检测到无效的后台配置</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **可能的原因**    | 为 Azure 备份正确指定支持参数 |
| **建议的操作** | 检查是否设置了以下（背面）参数：<br/>\*[catalog_backup_using_backint：真]<br/>\*[enable_accumulated_catalog_backup：假]<br/>\*[parallel_data_backup_backint_channels：1]<br/>\*[log_backup_timeout_s：900）]<br/>\*[backint_response_timeout：7200]<br/>如果 HOST 中存在基于背面的参数，请删除它们。 如果参数在 HOST 级别不存在，但在数据库级别已手动修改，请将其还原为前面所述的适当值。 或者，运行[停止保护并从](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database)Azure 门户保留备份数据，然后选择 **"恢复备份**"。 |

## <a name="restore-checks"></a>还原检查

### <a name="single-container-database-sdc-restore"></a>单个容器数据库 （SDC） 还原

在将 HANA 的单个容器数据库 （SDC） 还原到另一台 SDC 计算机时，请处理输入。 数据库名称应使用小写字母命名，并在括号中附加"sdc"。 HANA 实例将以大写字母显示。

假设 SDC HANA 实例"H21"已备份。 备份项页将显示备份项名称为 **"h21（sdc）"。** 如果尝试将此数据库还原到另一个目标 SDC（例如 H11），则需要提供以下输入。

![已还原的 SDC 数据库名称](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

请注意以下几点：

- 默认情况下，还原的 db 名称将填充备份项名称。 在这种情况下，h21（sdc）。
- 选择目标为 H11 不会自动更改还原的 db 名称。 **应将其编辑为 h11（sdc）。** 关于 SDC，还原的 db 名称将是带有小写字母和括号中"sdc"的目标实例 ID。
- 由于 SDC 只能有单个数据库，因此还需要单击该复选框以允许使用恢复点数据覆盖现有数据库数据。
- Linux 区分大小写。 因此，请小心保留案件。

### <a name="multiple-container-database-mdc-restore"></a>多个容器数据库 （MDC） 还原

在 HANA 的多个容器数据库中，标准配置为 SYSTEMDB = 1 或更多租户 GB。 还原整个 SAP HANA 实例意味着还原 SYSTEMDB 和租户 DB。 一个还原 SYSTEMDB，然后继续为租户数据库。 系统 DB 实质是指覆盖所选目标的系统信息。 此还原还覆盖目标实例中的 BackInt 相关信息。 因此，在系统 DB 还原到目标实例后，请再次运行预注册脚本。 只有这样，后续租户数据库还原才会成功。

## <a name="upgrading-from-sap-hana-10-to-20"></a>从 SAP HANA 1.0 升级到 2.0

如果要保护 SAP HANA 1.0 数据库并希望升级到 2.0，则执行以下步骤：

- 使用旧 SDC 数据库保留数据[停止保护](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)。
- 执行升级。 完成后，HANA 现在为 MDC，具有系统数据库和租户数据库。
- 重新运行[预注册脚本](https://aka.ms/scriptforpermsonhana)，包含 （sid 和 mdc） 的正确详细信息。
- 在 Azure 门户中重新注册同一计算机的扩展（备份->视图详细信息 ->选择相关的 Azure VM->重新注册）。
- 单击"重新发现同一 VM 的 DB"。 此操作应在步骤 2 中显示具有正确详细信息的新 DB（SYSTEMDB 和租户数据库，而不是 SDC）。
- 为这些新数据库配置备份。

## <a name="upgrading-without-an-sid-change"></a>升级时无需更改 SID

不会导致 SID 更改的操作系统或 SAP HANA 的升级可以按照以下方式进行处理：

- 使用数据库保留数据[停止保护](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)
- 执行升级。
- 重新运行[预注册脚本](https://aka.ms/scriptforpermsonhana)。 通常，我们已经看到升级过程删除了必要的角色。 运行预注册脚本将有助于验证所有必需的角色。
- 再次[恢复数据库保护](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database)

## <a name="re-registration-failures"></a>重新注册失败

在触发重新注册操作之前，请检查是否存在以下一种或多种症状：

- 所有操作（如备份、还原和配置备份）在 VM 上都失败，并且具有以下错误代码之一：**工作负载扩展不可访问、用户错误工作负载扩展未安装、工作负载扩展不存在、工作负载扩展未执行队列 Msg**。
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

- 查看有关在 Azure VM 上备份 SAP HANA 数据库[的常见问题](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm)。

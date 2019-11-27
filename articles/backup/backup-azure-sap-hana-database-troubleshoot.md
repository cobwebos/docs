---
title: 排查 SAP HANA 数据库备份错误
description: 介绍如何对使用 Azure 备份来备份 SAP HANA 数据库时可能发生的常见错误进行故障排除。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 9c6e4c66d96b02c2d5b4b4fe70fe6e6798c4e2c6
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285931"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>排查 Azure 上 SAP HANA 数据库的备份问题

本文提供了有关在 Azure 虚拟机上备份 SAP HANA 数据库的疑难解答信息。 有关当前支持的 SAP HANA 备份方案的详细信息，请参阅[方案支持](sap-hana-backup-support-matrix.md#scenario-support)。

## <a name="prerequisites-and-permissions"></a>先决条件和权限

在配置备份之前，请参阅[先决条件](tutorial-backup-sap-hana-db.md#prerequisites)和[设置权限](tutorial-backup-sap-hana-db.md#setting-up-permissions)部分。

## <a name="common-user-errors"></a>常见用户错误

| Error                                | 错误消息                    | 可能的原因                                              | 建议的操作                                           |
| ------------------------------------ | -------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| UserErrorInOpeningHanaOdbcConnection | 未能连接到 HANA 系统 | SAP HANA 实例可能已关闭。 <br> 未设置 Azure 备份与 HANA 数据库进行交互所需的权限。 | 检查 SAP HANA 数据库是否已启动。 如果数据库已启动并正在运行，请检查是否设置了所有必需的权限。 如果缺少任何权限，请运行[预先注册脚本](https://aka.ms/scriptforpermsonhana)以添加缺少的权限。 |
| UserErrorHanaInstanceNameInvalid | 指定的 SAP HANA 实例无效或找不到 | 无法备份单个 Azure VM 上的多个 SAP HANA 实例。 | 在要备份的 SAP HANA 实例上运行[预先注册脚本](https://aka.ms/scriptforpermsonhana)。 如果问题仍然存在，请联系 Microsoft 支持部门。 |
| UserErrorHanaUnsupportedOperation | 不支持指定的 SAP HANA 操作 | 适用于 SAP HANA 的 Azure 备份不支持对 SAP HANA 本机客户端（Studio/考核中心考核中心）执行增量备份和操作 | 有关详细信息，请参阅[此处](sap-hana-backup-support-matrix.md#scenario-support)。 |
| UserErrorHANAPODoesNotSupportBackupType | 此 SAP HANA 数据库不支持所请求的备份类型 | Azure 备份不支持使用快照进行增量备份和备份 | 有关详细信息，请参阅[此处](sap-hana-backup-support-matrix.md#scenario-support)。 |
| UserErrorHANALSNValidationFailure | 备份日志链中断 | 日志备份目标可能已从 backint 更新为文件系统，或者可能已更改 backint 可执行文件 | 触发完整备份以解决此问题 |
| UserErrorIncomaptibleSrcTargetSystsemsForRestore | 用于还原的源系统和目标系统不兼容 | 用于还原的目标系统与源系统不兼容 | 请参阅 SAP 说明[1642148](https://launchpad.support.sap.com/#/notes/1642148) ，了解当前支持的还原类型 |
| UserErrorSDCtoMDCUpgradeDetected | 检测到 MDC 升级 SDC | SAP HANA 实例已从 SDC 升级到 MDC。 更新后，备份会失败。 | 按照[从 SAP HANA 1.0 到2.0 部分](#upgrading-from-sap-hana-10-to-20)中列出的步骤进行操作以解决问题 |
| UserErrorInvalidBackintConfiguration | 检测到无效的 backint 配置 | 不正确地为 Azure 备份指定了后备参数 | 检查是否设置了以下（backint）参数： <br> * [catalog_backup_using_backint： true] <br>  * [enable_accumulated_catalog_backup： false] <br> * [parallel_data_backup_backint_channels： 1] <br>* [log_backup_timeout_s：900）] <br> * [backint_response_timeout： 7200] <br> 如果主机中存在基于 backint 的参数，请将其删除。 如果参数不存在于主机级别，但在数据库级别手动修改，请将它们还原到前面所述的相应值。 或者，运行 "[停止保护" 并保留](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)Azure 门户中的备份数据，然后选择 "**恢复备份**"。 |

## <a name="restore-checks"></a>还原检查

### <a name="single-container-database-sdc-restore"></a>单容器数据库（SDC）还原

在将 HANA 的单个容器数据库（SDC）还原到另一个 SDC 计算机时执行输入。 数据库名称应以小写形式提供，并在括号中追加 "sdc"。 HANA 实例将以大写字母显示。

假设已备份 SDC HANA 实例 "H21"。 "备份项" 页面会将备份项名称显示为 **"h21 （sdc）"** 。 如果尝试将此数据库还原到另一个目标 SDC （如 H11），则需要提供以下输入。

![SDC 还原输入](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

注意以下几点：

- 默认情况下，还原的数据库名称将用备份项名称（即 h21 （sdc））填充。
- 选择目标为 "H11" 不会自动更改还原的数据库名称。 **应将其编辑为 h11 （sdc）** 。 对于 SDC，还原的数据库名称将是带有小写字母的目标实例 ID，并在括号中追加了 "SDC"。
- 由于 SDC 只能有一个数据库，因此还需要单击该复选框以允许使用恢复点数据覆盖现有的数据库数据。
- Linux 区分大小写。 因此，请注意保留大小写。

### <a name="multiple-container-database-mdc-restore"></a>多容器数据库（MDC）还原

在 HANA 的多个容器数据库中，标准配置为 SYSTEMDB + 1 个或更多租户数据库。 还原整个 SAP HANA 实例意味着还原 SYSTEMDB 和租户数据库。 首先还原 SYSTEMDB，然后继续进行租户数据库。 系统 DB 实质上是指覆盖所选目标上的系统信息。 此还原还会替代目标实例中的与 BackInt 相关的信息。 因此，将系统数据库还原到目标实例后，需要再次运行预注册脚本。 只有这样，随后的租户数据库还原才会成功。

## <a name="upgrading-from-sap-hana-10-to-20"></a>从 SAP HANA 1.0 升级到2。0

如果要保护 SAP HANA 1.0 数据库，并希望升级到2.0，请执行下面所述的步骤：

- 对于旧的 SDC 数据库，[停止保护](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)并保留数据。
- 重新运行具有（sid 和 mdc）详细信息的[预注册脚本](https://aka.ms/scriptforpermsonhana)。
- 重新注册扩展（备份 > 视图详细信息-> 选择相关的 Azure VM > 重新注册）。
- 单击 "重新发现同一 VM 的数据库"。 此操作应显示第2步中的新数据库，其中包含正确的详细信息（SYSTEMDB 和租户 DB，而不是 SDC）。
- 保护这些新数据库。

## <a name="upgrading-without-an-sid-change"></a>升级而不更改 SID

不会导致 SID 更改的操作系统或 SAP HANA 的升级可按如下所述进行处理：

- [停止保护](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)并保留数据库的数据
- 重新运行[预注册脚本](https://aka.ms/scriptforpermsonhana)
- 再次为数据库[恢复保护](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database)

## <a name="next-steps"></a>后续步骤

- 查看有关在 Azure Vm 上备份 SAP HANA 数据库[的常见问题]](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm)

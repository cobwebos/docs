---
title: 关于 Azure VM 中的 SAP HANA 数据库备份
description: 在本文中，了解备份在 Azure 虚拟机上运行的 SAP HANA 数据库。
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 52c235c95cea73a0c51c62fcb55f7f711d2eff21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476451"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>关于 Azure VM 中的 SAP HANA 数据库备份

SAP HANA 数据库是任务关键型工作负载，需要低恢复点目标 （RPO） 和快速恢复时间目标 （RTO）。 现在可以使用[Azure 备份](https://docs.microsoft.com/azure/backup/backup-overview)[备份备份在 Azure VM 上运行的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db)。

Azure 备份由 SAP[认证](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5)，通过利用 SAP HANA 的本机 API 提供本机备份支持。 Azure 备份的此产品与 Azure 备份的**零基础结构**备份口号一致，无需部署和管理备份基础结构。 现在，您可以无缝备份和恢复在 Azure VM 上运行的 SAP HANA 数据库（现在也支持[M 系列 VM！），](../virtual-machines/m-series.md)并利用 Azure 备份提供的企业管理功能。

## <a name="added-value"></a>附加值

使用 Azure 备份来备份和恢复 SAP HANA 数据库，具有以下优点：

* **15 分钟恢复点目标 （RPO）：** 现在有可能恢复长达 15 分钟的关键数据。
* **一键式，时间点还原**：将生产数据还原到备用 HANA 服务器变得容易。 备份和目录的链条执行还原都由 Azure 在后台管理。
* **长期保留**：满足严格的合规性和审计需求。 根据保留期保留备份数年，超过此时间，恢复点将由内置生命周期管理功能自动压缩。
* **Azure 的备份管理**：使用 Azure 备份的管理和监视功能来改进管理体验。 Azure CLI 也受支持。

要查看我们今天支持的备份和还原方案，请参阅[SAP HANA 方案支持矩阵](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)。

## <a name="backup-architecture"></a>备份体系结构

![备份体系结构图](./media/sap-hana-db-about/backup-architecture.png)

* 备份过程首先在 Azure 中[创建恢复服务保管库](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault)。 此保管库将用于存储随时间创建的备份和恢复点。
* 运行 SAP HANA 服务器的 Azure VM 已注册到保管库，并[发现](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases)要备份的数据库。 要使 Azure 备份服务能够发现数据库，必须在 HANA 服务器上作为根用户运行[预注册脚本](https://aka.ms/scriptforpermsonhana)。
* 此脚本在**hdbuser 存储**中创建**AZUREWLBACKUPHANAUSER** DB 用户和同名的相应密钥。 请参阅[预注册脚本的作用](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)部分，以了解有关脚本的作用的详细信息。
* Azure 备份服务现在在已注册的 SAP HANA 服务器上安装用于 HANA 的**Azure 备份插件**。
* 由预注册脚本创建的**AZUREWLBACKUPHANAUSER** DB 用户由用于**HANA 的 Azure 备份插件**用于执行所有备份和还原操作。 如果您尝试在不运行此脚本的情况下为 SAP HANA DB 配置备份，您可能会收到以下错误 **：UserErrorHanaScriptNotRun**。
* 要[配置](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup)发现的数据库上的备份，请选择所需的备份策略并启用备份。

* 配置备份后，Azure 备份服务在受保护的 SAP HANA 服务器上的 DATABASE 级别设置以下 Backint 参数：
  * [catalog_backup_using_backint：真]
  * [enable_accumulated_catalog_backup：假]
  * [parallel_data_backup_backint_channels：1]
  * [log_backup_timeout_s：900）]
  * [backint_response_timeout：7200]

>[!NOTE]
>确保这些*参数不在*HOST 级别存在。 主机级参数将覆盖这些参数，并可能导致意外行为。
>

* **HANA 的 Azure 备份插件**维护所有备份计划以及策略详细信息。 它触发计划备份，并通过 Backint API 与**HANA 备份引擎**通信。
* **HANA 备份引擎**返回具有要备份数据的 Backint 流。
* 所有计划备份和按需备份（从 Azure 门户触发）都是完整备份或差异备份，由**HANA 的 Azure 备份插件**启动。 但是，日志备份由**HANA 备份引擎**本身管理和触发。
* SAP HANA 的 Azure 备份是 BackInt 认证的解决方案，不依赖于基础磁盘或 VM 类型。 备份由 HANA 生成的流执行。

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>将 Azure VM 备份与 Azure SAP HANA 备份一起使用

除了在 Azure 中使用提供数据库级备份和恢复的 SAP HANA 备份外，还可以使用 Azure VM 备份解决方案备份操作系统和非数据库磁盘。

[经过 Backint 认证的 Azure SAP HANA 备份解决方案](#backup-architecture)可用于数据库备份和恢复。

[Azure VM 备份](backup-azure-vms-introduction.md)可用于备份操作系统和其他非数据库磁盘。 VM 备份每天执行一次，并备份所有磁盘（**写入加速器 （WA） 磁盘**和**UltraDisk**除外）。 由于正在使用 Azure SAP HANA 备份解决方案备份数据库，因此可以使用当前处于预览状态的排除磁盘功能仅对操作系统和非数据库磁盘进行文件一致的备份。

>[!NOTE]
> 将预发布脚本与 Azure VM 备份一起使用将允许对数据库的数据卷进行应用一致的备份。 但是，如果日志区域驻留在 WA 磁盘上，则拍摄这些磁盘的快照可能不能保证日志区域的一致性。 HANA 具有生成日志备份的明确方法，原因正是如此。 在 SAP HANA 中启用相同的功能，可以使用 Azure SAP HANA 备份来备份它们。

要还原运行 SAP HANA 的 VM，请按照以下步骤操作：

* [从 Azure VM 备份从最新恢复点还原新 VM。](backup-azure-arm-restore-vms.md) 或者创建新的空 VM 并从最新的恢复点连接磁盘。
* 由于未备份 WA 磁盘，因此不会还原它们。 创建空 WA 磁盘和日志区域。
* 设置所有其他配置（如 IP、系统名称等）后，VM 将设置为从 Azure 备份接收数据库数据。
* 现在，将数据库从[Azure SAP HANA 数据库备份](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point)还原到 VM 到所需的时间点。

## <a name="next-steps"></a>后续步骤

* 了解如何[还原在 Azure VM 上运行的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* 了解如何[管理使用 Azure 备份进行备份的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)

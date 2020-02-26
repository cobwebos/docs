---
title: 关于在 Azure Vm 中 SAP HANA 数据库备份
description: 本文介绍如何备份 Azure 虚拟机上运行的 SAP HANA 数据库。
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: a1be572d6462ed8f8a86db0f5cbfeaaa37c219ab
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586557"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>关于在 Azure Vm 中 SAP HANA 数据库备份

SAP HANA 数据库是需要低恢复点目标（RPO）和快速恢复时间目标（RTO）的任务关键型工作负荷。 你现在可以使用[Azure 备份](https://docs.microsoft.com/azure/backup/backup-overview)来[备份 Azure vm 上运行的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db)。

Azure 备份通过 SAP[认证](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5)，通过利用 SAP HANA 的本机 api 提供本机备份支持。 Azure 备份中的此产品/服务可与 Azure 备份的**零基础结构**备份口头禅，从而无需部署和管理备份基础结构。 你现在可以无缝备份和还原 SAP HANA 在 Azure Vm 上运行的数据库（现在还支持[M 系列 vm](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory) ！），并利用 Azure 备份提供的企业管理功能。

## <a name="added-value"></a>添加的值

使用 Azure 备份来备份和还原 SAP HANA 数据库可提供以下优势：

* **15 分钟的恢复点目标（RPO）** ：现在可以恢复关键数据（最多15分钟）。
* **一次单击，时间点还原**：将生产数据还原到备用 HANA 服务器。 用于执行还原的备份和目录链接都由 Azure 在幕后进行管理。
* 长期**保留**：满足严格的相容性和审核需求。 基于保留期保留你的备份多年，超过此时间后，将通过内置生命周期管理功能自动修剪恢复点。
* **Azure 中的备份管理**：使用 azure 备份的管理和监视功能来改进管理体验。 还支持 Azure CLI。

若要查看目前支持的备份和还原方案，请参阅[SAP HANA 方案支持矩阵](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)。

## <a name="backup-architecture"></a>备份体系结构

![备份体系结构关系图](./media/sap-hana-db-about/backup-architecture.png)

* 备份过程首先在 Azure 中[创建恢复服务保管库](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault)。 此保管库将用于存储一段时间内创建的备份和恢复点。
* 运行 SAP HANA server 的 Azure VM 向保管库注册，并[发现](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases)要备份的数据库。 若要使 Azure 备份服务能够发现数据库，必须在 HANA 服务器上以 root 用户身份运行[预先注册脚本](https://aka.ms/scriptforpermsonhana)。
* 此脚本在**hdbuserstore**中创建**AZUREWLBACKUPHANAUSER** DB 用户和具有相同名称的相应键。 若要详细了解脚本的作用，请参阅[设置权限部分](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#setting-up-permissions)。
* Azure 备份服务现在在已注册的 SAP HANA 服务器上安装**适用于 HANA 的 Azure 备份插件**。
* 由预先注册脚本创建的**AZUREWLBACKUPHANAUSER** DB 用户由用于**HANA 的 Azure 备份插件**用于执行所有备份和还原操作。 如果尝试在不运行此脚本的情况下为 SAP HANA Db 配置备份，可能会收到以下错误： **UserErrorHanaScriptNotRun**。
* 若要在发现的数据库上[配置备份](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup)，请选择所需的备份策略并启用备份。

* 配置备份后，Azure 备份服务将在受保护的 SAP HANA 服务器上的数据库级别设置以下 Backint 参数：
  * [catalog_backup_using_backint:true]
  * [enable_accumulated_catalog_backup:false]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>确保这些参数*不*存在于主机级别。 主机级别参数将重写这些参数，并可能导致意外的行为。
>

* **适用于 HANA 的 Azure 备份插件**维护所有备份计划和策略详细信息。 它通过 Backint Api 触发计划的备份，并与**HANA 备份引擎**通信。
* **HANA 备份引擎**返回具有要备份的数据的 Backint 流。
* 所有计划的备份和按需备份（由 Azure 门户触发）完全或差异均由**适用于 HANA 的 Azure 备份插件**启动。 但是，日志备份由**HANA 备份引擎**自身管理并触发。

## <a name="next-steps"></a>后续步骤

* 了解如何[还原在 AZURE VM 上运行的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* 了解如何[管理使用 Azure 备份进行备份的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)

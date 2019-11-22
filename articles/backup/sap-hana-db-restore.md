---
title: 在 Azure Vm 上还原 SAP HANA 数据库
description: 本文介绍如何还原在 Azure 虚拟机上运行的 SAP HANA 数据库。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287913"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>在 Azure Vm 上还原 SAP HANA 数据库

本文介绍如何还原 azure 备份服务已备份到 Azure 备份恢复服务保管库的 Azure 虚拟机（VM）上运行的 SAP HANA 数据库。 还原可用于为开发/测试方案创建数据的副本，或返回到以前的状态。

有关如何备份 SAP HANA 数据库的详细信息，请参阅在[Azure vm 上备份 SAP HANA 的数据库](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>还原到某个时间点或恢复点

Azure 备份可以还原在 Azure Vm 上运行 SAP HANA 数据库，如下所示：

* 使用日志备份还原到特定的日期或时间（到第二个）。 Azure 备份会自动根据所选时间确定所需的完整差异备份和日志备份链。

* 还原到特定的完整备份或差异备份以还原到特定恢复点。

## <a name="prerequisites"></a>先决条件

在还原数据库之前，请注意以下事项：

* 只能将数据库还原到位于同一区域中的 SAP HANA 实例

* 目标实例必须注册到与源相同的保管库

* Azure 备份不能识别同一 VM 上两个不同的 SAP HANA 实例。 因此，不能将数据从同一 VM 上的一个实例还原到另一个实例

* 若要确保目标 SAP HANA 实例已准备好进行还原，请检查其**备份就绪**状态：

  * 打开要在其中注册目标 SAP HANA 实例的保管库

  * 在保管库仪表板上的 **"入门" 下，** 选择 "**备份**"

![保管库仪表板中的备份](media/sap-hana-db-restore/getting-started-backup.png)

* 在 "**备份**" 下，在 "**要备份什么？** " 下，选择**Azure VM 中的 SAP HANA**

![选择 Azure VM 中的 SAP HANA](media/sap-hana-db-restore/sap-hana-backup.png)

* 在**发现 vm 中的 db 下，** 单击 "**查看详细信息**"

![查看详细信息](media/sap-hana-db-restore/view-details.png)

* 查看目标 VM 的**备份准备情况**

![受保护的服务器](media/sap-hana-db-restore/protected-servers.png)

* 若要详细了解 SAP HANA 支持的还原类型，请参阅 SAP HANA 注释[1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>还原数据库

* 打开要在其中还原 SAP HANA 数据库的保管库

* 在保管库仪表板上的 "**受保护项**" 下，选择 "**备份项**"

![备份项](media/sap-hana-db-restore/backup-items.png)

* 在 "备份**项**" 下的 "**备份管理类型**" 下，选择**Azure VM 中的 SAP HANA**

![备份管理类型](media/sap-hana-db-restore/backup-management-type.png)

* 选择要还原的数据库

 ![要还原的数据库](media/sap-hana-db-restore/database-to-restore.png)

* 查看数据库菜单。 它提供有关数据库备份的信息，包括：

  * 最早和最新的还原点

  * 数据库的过去24和72小时的日志备份状态

![数据库菜单](media/sap-hana-db-restore/database-menu.png)

* 选择**还原数据库**

* 在 "**还原配置**" 下，指定还原数据的位置（或方法）：

  * **备用位置**：将数据库还原到备用位置，并保留原始源数据库。

  * **覆盖数据库**：将数据还原到与原始源相同的 SAP HANA 实例。 此选项将覆盖原始数据库。

![还原配置](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>还原到备用位置

* 在 "**还原配置**" 菜单中的 "**还原位置**" 下，选择 "**备用位置**"。

![还原到备用位置](media/sap-hana-db-restore/restore-alternate-location.png)

* 选择要将数据库还原到的 SAP HANA 主机名和实例名。
* 检查目标 SAP HANA 实例是否已准备好进行还原，方法是确保其**备份就绪状态。** 有关更多详细信息，请参阅[先决条件部分](#prerequisites)。
* 在“还原数据库名称”框中，输入目标数据库的名称。

> [!NOTE]
> 单一数据库容器（SDC）还原必须遵循这些[检查](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)。

* 如果适用，**如果所选 HANA 实例上已存在具有相同名称的数据库，请选择 "覆盖"** 。
* 选择“确定”。

![还原配置-最终屏幕](media/sap-hana-db-restore/restore-configuration-last.png)

* 在 "**选择还原点**" 中，选择要[还原到特定时间点的](#restore-to-a-specific-point-in-time)**日志（时间点）** 。 或者选择 "**完全 & 差异**" 以[还原到特定恢复点](#restore-to-a-specific-recovery-point)。

### <a name="restore-and-overwrite"></a>还原并覆盖

* 在 "**还原配置**" 菜单中的 "**还原位置**" 下，选择 "**覆盖 DB** >  **" 确定 "** 。

![覆盖 DB](media/sap-hana-db-restore/overwrite-db.png)

* 在 "**选择还原点**" 中，选择要[还原到特定时间点的](#restore-to-a-specific-point-in-time)**日志（时间点）** 。 或者选择 "**完全 & 差异**" 以[还原到特定恢复点](#restore-to-a-specific-recovery-point)。

### <a name="restore-to-a-specific-point-in-time"></a>还原到特定时间点

如果已选择“日志(时间点)”作为还原类型，请执行以下操作：

* 从日志图中选择一个恢复点，然后选择 **"确定"** 以选择还原的时间点。

![还原点](media/sap-hana-db-restore/restore-point.png)

* 在“还原”菜单中，选择“还原”启动还原作业。

![选择还原](media/sap-hana-db-restore/restore-restore.png)

* 通过选择 "数据库" 菜单上的 "**还原作业**"，在 "**通知**" 区域中跟踪还原进度或跟踪进度。

![已成功触发还原](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>还原到特定的恢复点

如果已选择“完整和差异”作为还原类型，请执行以下操作：

* 从列表中选择一个恢复点，然后选择 **"确定"** 以选择还原的时间点。

![还原特定恢复点](media/sap-hana-db-restore/specific-recovery-point.png)

* 在“还原”菜单中，选择“还原”启动还原作业。

![启动还原作业](media/sap-hana-db-restore/restore-specific.png)

* 通过选择 "数据库" 菜单上的 "**还原作业**"，在 "**通知**" 区域中跟踪还原进度或跟踪进度。

![还原进度](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> 在将系统数据库还原到目标实例之后，多数据库容器（MDC）还原时，需要再次运行预注册脚本。 只有这样，随后的租户数据库还原才会成功。 若要了解详细信息，请参阅[故障排除– MDC 还原](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore)。

## <a name="next-steps"></a>后续步骤

* [了解如何](sap-hana-db-manage.md)使用 Azure 备份管理备份 SAP HANA 数据库

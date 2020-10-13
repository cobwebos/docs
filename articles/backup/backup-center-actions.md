---
title: 使用备份中心执行操作
description: 本文介绍如何使用备份中心执行操作
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 65ac6374b1a9571d0415bc097a6ee957874d3d44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91854420"
---
# <a name="perform-actions-using-backup-center"></a>使用备份中心执行操作

利用备份中心，无需导航到单个保管库，即可执行关键的与备份相关的操作。 可以从备份中心执行的操作包括：

* 为数据源配置备份
* 还原备份实例
* 创建新的保管库
* 创建新备份策略
* 触发备份实例的按需备份
* 停止备份实例备份

## <a name="supported-scenarios"></a>支持的方案

* 目前，Azure VM 备份和 Azure Database for PostgreSQL 服务器备份支持备份中心。
* 有关支持的和不支持的方案的详细列表，请参阅 [支持矩阵](backup-center-support-matrix.md) 。

## <a name="configure-backup"></a>配置备份

根据你想要备份的数据源的类型，请按照下面所述的相应说明进行操作。

### <a name="configure-backup-for-azure-virtual-machines"></a>为 Azure 虚拟机配置备份

1. 导航到 "备份中心"，然后选择 "**概述**" 选项卡顶部的 " **+ 备份**"。

    ![备份中心概述](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. 在本例中，选择要备份的数据源的类型 (Azure 虚拟机) 。

    ![选择要配置 VM 备份的数据源](./media/backup-center-actions/backup-select-datasource-vm.png)

3. 选择恢复服务保管库，并选择 " **继续**"。 这会使你获得与从恢复服务保管库可访问的配置相同的备份配置体验。 [详细了解如何使用恢复服务保管库配置 Azure 虚拟机的备份](tutorial-backup-vm-at-scale.md)。

### <a name="configure-backup-for-azure-database-for-postgresql-server"></a>为 Azure Database for PostgreSQL Server 配置备份

1. 导航到 "备份中心"，然后选择 "**概述**" 选项卡顶部的 " **+ 备份**"。
2. 在这种情况下，选择要备份的数据源的类型 (Azure Database for PostgreSQL 服务器) 。

    ![选择要配置 Azure Database for PostgreSQL 服务器备份的数据源](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. 选择“继续”。 这会使你获得与从备份保管库中可访问的配置相同的备份配置体验。 [详细了解如何使用备份保管库配置 Azure Database for PostgreSQL Server 的备份](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)。

## <a name="restore-a-backup-instance"></a>还原备份实例

根据要还原的数据源的类型，请按照下面所述的相应说明进行操作。

### <a name="if-youre-restoring-an-azure-virtual-machine"></a>如果要还原 Azure 虚拟机

1. 导航到 "备份中心"，然后选择 "**概述**" 选项卡顶部的 "**还原**"。

    ![备份中心概述恢复 VM](./media/backup-center-actions/backup-center-overview-restore.png)

2. 在此示例中，选择要在 Azure 虚拟机 (还原的数据源的类型) 。

    ![选择 VM 还原的数据源](./media/backup-center-actions/restore-select-datasource-vm.png)

3. 选择一个备份实例，然后选择 " **继续**"。 这会使你转到与从恢复服务保管库可访问的设置相同的还原设置体验。 [了解有关如何使用恢复服务保管库还原 Azure 虚拟机的详细信息](backup-azure-arm-restore-vms.md#before-you-start)。

### <a name="if-youre-restoring-an-azure-database-for-postgresql-server"></a>如果要还原 Azure Database for PostgreSQL 服务器

1. 导航到 "备份中心"，然后选择 "**概述**" 选项卡顶部的 "**还原**"。
2. 选择要在这种情况下 Azure Database for PostgreSQL 服务器 (还原的数据源的类型) 。

    ![选择 Azure Database for PostgreSQL 服务器还原的数据源](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. 选择一个备份实例，然后选择 " **继续**"。 这会使你转到与从恢复服务保管库可访问的设置相同的还原设置体验。 [详细了解如何使用备份保管库还原 Azure Database for PostgreSQL 服务器](backup-azure-database-postgresql.md#restore)。

## <a name="create-a-new-vault"></a>创建新的保管库

可以通过导航到 "备份中心" 并选择 "**概述**" 选项卡顶部的 " **+ 保管库**" 来创建新的保管库。

![创建保管库](./media/backup-center-actions/backup-center-create-vault.png)

* [了解有关创建恢复服务保管库的详细信息](backup-create-rs-vault.md)
* [了解有关创建备份保管库的详细信息](backup-vault-overview.md)

## <a name="create-a-new-backup-policy"></a>创建新备份策略

根据要备份的数据源的类型，请按照下面所述的相应说明进行操作。

### <a name="if-youre-backing-up-an-azure-virtual-machine"></a>如果要备份 Azure 虚拟机

1. 导航到 "备份中心"，然后选择 "**概述**" 选项卡顶部的 " **+ 策略**"。

    ![备份策略的备份中心概述](./media/backup-center-actions/backup-center-overview-policy.png)

2. 在本例中，选择要备份的数据源的类型 (Azure 虚拟机) 。

    ![为 VM 备份选择策略的数据源](./media/backup-center-actions/policy-select-datasource-vm.png)

3. 选择恢复服务保管库，并选择 " **继续**"。 这会使你获得与从恢复服务保管库可访问的相同的策略创建体验。 [详细了解如何使用恢复服务保管库为 Azure 虚拟机创建新的备份策略](backup-azure-arm-vms-prepare.md#create-a-custom-policy)。

### <a name="if-youre-backing-up-an-azure-database-for-postgresql-server"></a>如果要备份 Azure Database for PostgreSQL 服务器

1. 导航到 "备份中心"，然后选择 "**概述**" 选项卡顶部的 " **+ 策略**"。
2. 在这种情况下，选择要备份的数据源的类型 (Azure Database for PostgreSQL 服务器) 。

    ![为 Azure Database for PostgreSQL 服务器备份的策略选择数据源](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. 选择“继续”。 这会使你获得与从备份保管库可访问的策略相同的策略创建体验。 [详细了解如何使用备份保管库创建新的备份策略](backup-azure-database-postgresql.md#create-backup-policy)。

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>执行备份实例的按需备份

备份中心允许在备份空间内搜索备份实例，并按需执行备份操作。

若要触发按需备份，请导航到 "备份中心"，然后选择 " **备份实例** " 菜单项。 选择此设置可查看有权访问的所有备份实例的详细信息。 可以搜索要备份的备份实例。 右键单击网格中的某个项会打开可用操作的列表。 选择 " **立即备份** " 选项可执行按需备份。

![按需备份](./media/backup-center-actions/backup-center-on-demand-backup.png)

[详细了解如何对 Azure 虚拟机执行按需备份](backup-azure-manage-vms.md#run-an-on-demand-backup)。

[详细了解如何针对 Azure Database for PostgreSQL 服务器执行按需备份](backup-azure-database-postgresql.md#on-demand-backup)。

## <a name="stop-backup-for-a-backup-instance"></a>停止备份实例备份

在某些情况下，你可能想要停止备份实例的备份，例如当要备份的基础资源不再存在时。

若要触发按需备份，请导航到 "备份中心"，然后选择 " **备份实例** " 菜单项。 选择此设置可查看有权访问的所有备份实例的详细信息。 可以搜索要备份的备份实例。 右键单击网格中的某个项会打开可用操作的列表。 选择 " **停止备份** " 选项以停止备份实例备份。

![停止保护](./media/backup-center-actions/backup-center-stop-protection.png)

[了解有关停止 Azure 虚拟机备份的详细信息](backup-azure-manage-vms.md#stop-protecting-a-vm)。

[详细了解停止 Azure Database for PostgreSQL 服务器的备份](backup-azure-database-postgresql.md#stop-protection)

## <a name="next-steps"></a>后续步骤

* [监视和操作备份](backup-center-monitor-operate.md)
* [控制备份场地](backup-center-govern-environment.md)
* [获取有关备份的见解](backup-center-obtain-insights.md)

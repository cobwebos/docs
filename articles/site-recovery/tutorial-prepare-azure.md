---
title: 创建用于 Azure Site Recovery 的资源 | Microsoft 文档
description: 了解如何使用 Azure Site Recovery 准备 Azure ，以进行本地计算机复制。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 95d6673acaf3cbac2098ac7ae30114696f477045
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212783"
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>准备 Azure 资源以进行本地计算机复制

 [Azure Site Recovery](site-recovery-overview.md) 通过在计划内和计划外中断期间使商业应用程序保持启动和运行状态，有助于实施业务连续性和灾难恢复 (BCDR) 策略。 Site Recovery 管理并安排本地计算机和 Azure 虚拟机 (VM) 的灾难恢复，包括复制、故障转移和恢复。

本教程介绍如何在将本地 VM（Hyper-V 或 VMware）或 Windows/Linux 物理服务器复制到 Azure 时准备 Azure 组件。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 验证 Azure 帐户是否具有复制权限。
> * 创建 Azure 存储帐户。 复制的数据存储在其中。
> * 创建恢复服务保管库。
> * 设置 Azure 网络。 在故障转移后创建的 Azure VM 会加入此 Azure 网络。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](http://portal.azure.com)。

## <a name="verify-account-permissions"></a>验证帐户权限

如果你刚刚创建了免费 Azure 帐户，那么你就是订阅的管理员。 如果你不是订阅管理员，请要求管理员分配你所需的权限。 若要为新虚拟机启用复制，必须有权执行以下操作：

- 在所选资源组中创建 VM。
- 在所选虚拟网络中创建 VM。
- 向所选存储帐户进行写入。

若要完成这些任务，应为帐户分配“虚拟机参与者”内置角色。 此外，若要在保管库中管理 Site Recovery 操作，应为帐户分配“Site Recovery 参与者”内置角色。

## <a name="create-a-storage-account"></a>创建存储帐户

已复制计算机的映像保存在 Azure 存储中。 在从本地故障转移到 Azure 时，会从该存储中创建 Azure VM。

1. 在 [Azure 门户](https://portal.azure.com)菜单中，选择“新建” > “存储” > “存储帐户”。
2. 在“创建存储帐户”中，输入帐户的名称。 对于这些教程，我们使用 **contosovmsacct1910171607**。 所选名称在 Azure 中必须唯一，长度介于 3-24 个字符，且仅包含数字和小写字母。
3. 在“部署模型”中，选择“资源管理器”。
4. 在“帐户类型”中，选择“常规用途”。 在“性能”中，选择“标准”。 请不要选择 blob 存储。
5. 在“复制”中，选择默认的“读取访问异地冗余存储”作为存储冗余。
6. 在“订阅”中，选择要在其中创建新存储帐户的订阅。
7. 在“资源组”中，输入新的资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 在本系列教程中，请使用名称“ContosoRG”。
8. 在“位置”中，选择存储帐户的地理位置。 存储帐户必须位于与恢复服务保管库相同的区域。 在这些教程中，请使用“西欧”区域。

   ![创建存储帐户](media/tutorial-prepare-azure/create-storageacct.png)

9. 选择“创建”以创建存储帐户。

## <a name="create-a-vault"></a>创建保管库

1. 在 Azure 门户中，选择“创建资源” > “监视 + 管理” > “备份和站点恢复”。
2. 在“名称”中，输入一个友好名称以标识此保管库。 对于这组教程，我们使用 **ContosoVMVault**。
3. 在“资源组”中，选择名为 contosoRG 的现有资源组。
4. 在“位置”中，输入在本教程系列中使用的 Azure 地区“西欧”。
5. 若要从仪表板快速访问保管库，请选择“固定到仪表板” > “创建”。

   ![创建新的保管库](./media/tutorial-prepare-azure/new-vault-settings.png)

   新保管库显示在“仪表板” > “所有资源”中，以及“恢复服务保管库”主页上。

## <a name="set-up-an-azure-network"></a>设置 Azure 网络

在故障转移后创建的 Azure VM 会加入此网络。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源” > “网络” > “虚拟网络”。
2. 选择“资源管理器”作为部署模型。 建议使用“资源管理器”部署模型。 然后执行以下步骤：

   a. 在“名称”中，输入网络名称。 名称在 Azure 资源组中必须唯一。 使用名称“ContosoASRnet”。

   b. 在“资源组”中，使用现有的资源组 contosoRG。

   c. 在“地址范围”中，输入网络地址范围 **10.0.0.0/24**。

   d. 本教程中不需要子网。

   e. 在“订阅”中，选择要在其中创建网络的订阅。

   f. 在“位置”中，选择“西欧”。 该网络必须位于与恢复服务保管库相同的区域中。

3. 选择**创建**。

   ![创建虚拟网络](media/tutorial-prepare-azure/create-network.png)

   创建虚拟网络需要几秒钟的时间。 创建后，即可在 Azure 门户仪表板中看到它。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [准备本地 VMware基础结构以对 Azure 进行灾难恢复](tutorial-prepare-on-premises-vmware.md)

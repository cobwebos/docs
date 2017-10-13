---
title: "创建用于 Azure Site Recovery 的资源 | Microsoft 文档"
description: "了解如何使用 Azure Site Recovery 服务准备 Azure ，以进行本地计算机复制。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 321e304f-b29e-49e4-aa64-453878490ea7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 506b625905abf52963230a787af66f956bc292b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>准备 Azure 资源以进行本地计算机复制

[Azure Site Recovery](site-recovery-overview.md) 服务通过在计划内和计划外停机期间使商业应用程序保持启动和运行可用状态，有助于实施业务连续性和灾难恢复 (BCDR) 策略。 Site Recovery 管理并安排本地计算机和 Azure 虚拟机 (VM) 的灾难恢复，包括复制、故障转移和恢复。

本教程将介绍如何在将本地虚拟机和物理服务器复制到 Azure 时准备 Azure 组件。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 验证帐户具有复制权限
> * 创建 Azure 存储帐户。
> * 设置 Azure 网络。 在故障转移后创建的 Azure VM 会加入此 Azure 网络。

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="log-in-to-azure"></a>登录 Azure

通过 http://portal.azure.com 登录到 Azure 门户。

## <a name="verify-account-permissions"></a>验证帐户权限

如果你刚刚创建了免费 Azure 帐户，那么你就是订阅的管理员。 如果你不是订阅管理员，请与管理员一起分配你所需的权限。 若要为新虚拟机启用复制，必须满足：

- 在所选资源组中创建虚拟机的权限
- 在所选虚拟网络中创建虚拟机的权限
- 写入所选存储帐户的权限

“虚拟机参与者”内置角色具有这些权限。 你还需要管理 Azure Site Recovery 操作的权限。 “Site Recovery 参与者”角色拥有管理恢复服务保管库中 Site Recovery 操作所需的全部权限。

## <a name="create-a-storage-account"></a>创建存储帐户

已复制计算机的映像保存在 Azure 存储中。 在从本地故障转移到 Azure 时，会从该存储中创建 Azure VM。

1. 在 [Azure 门户](https://portal.azure.com)菜单中，单击“新建” -> “存储” -> “存储帐户”。
2. 输入存储帐户的名称。 在本系列教程中，我们将使用名称 contosovmsacct1910171607。 该名称在 Azure 中必须唯一，长度介于 3-24 个字符，并且仅包含数字和小写字母。
3. 使用资源管理器部署模型。
4. 选择“常规用途” > “标准”。
5. 为存储冗余选择默认的“RA-GRS”。
6. 选择想在其中创建新存储帐户的订阅。
7. 指定新的资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 在本系列教程中，我们将使用名称 ContosoRG。
8. 选择存储帐户的地理区域。 存储帐户必须位于与恢复服务保管库相同的区域。 对于这些教程中，我们将使用位置西欧。

   ![create-storageacct](media/tutorial-prepare-azure/create-storageacct.png)

9. 单击“创建”以创建存储帐户。

## <a name="create-a-vault"></a>创建保管库

1. 在 Azure 门户菜单中，单击 **新建** > **监视和管理** >
   **备份和站点恢复**。
2. 在“名称”中，指定一个友好名称以标识该保管库。 在本教程中，我们使用 ContosoVMVault。
3. 选择名为 contosoRG 的现有资源组。
4. 指定 Azure 区域为西欧。
5. 要从仪表板快速访问保管库，请单击“固定到仪表板” > “创建”。

   ![新保管库](./media/tutorial-prepare-azure/new-vault-settings.png)

   新保管库会显示在“仪表板” > “所有资源”中，以及“恢复服务保管库”主页面上。

## <a name="set-up-an-azure-network"></a>设置 Azure 网络

在故障转移后创建的 Azure VM 会加入此网络。

1. 在 [Azure 门户](https://portal.azure.com) 菜单中，单击 **新建** > **网络** >
   **虚拟网络**
2. 选择“资源管理器”作为部署模型。 建议使用“资源管理器”部署模型。
   - 指定网络名称。 名称在 Azure 资源组中必须唯一。 我们将使用名称 ContosoASRnet
   - 使用现有的资源组 contosoRG。
   - 指定网络地址范围 10.0.0.0/24/24。
   - 我们本教程中不需要子网。
   - 选择要在其中创建网络的订阅。
   - 选择位置“西欧”。 该网络必须位于与恢复服务保管库相同的区域中。
3. 单击“创建” 。

   ![create-network](media/tutorial-prepare-azure/create-network.png)

   创建虚拟网络需要几秒钟的时间。 创建后，即可在 Azure 门户仪表板中看到它。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [准备本地 VMware基础结构以对 Azure 进行灾难恢复](tutorial-prepare-on-premises-vmware.md)

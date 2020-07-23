---
title: 使用 Azure Site Recovery 设置到次要区域的 Azure VM 灾难恢复
description: 使用 Azure Site Recovery 服务快速为 Azure VM 设置到其他 Azure 区域的灾难恢复。
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: 8681ccaac30402927005e5e8802d7da2bddc2c5c
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135711"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>快速入门：为 Azure VM 设置到 Azure 次要区域的灾难恢复

[Azure Site Recovery](site-recovery-overview.md) 服务通过在计划内和计划外中断期间使商业应用程序保持联机状态，有助于实施业务连续性和灾难恢复 (BCDR) 策略。 Site Recovery 管理并安排本地计算机和 Azure 虚拟机 (VM) 的灾难恢复，包括复制、故障转移和恢复。

本快速入门介绍了如何通过将 Azure VM 复制到次要 Azure 区域来为其设置灾难恢复。 通常，使用默认设置来启用复制。

## <a name="prerequisites"></a>先决条件

需要 Azure 订阅和 VM 才能完成此教程。

- 如果没有一个订阅处于活动状态的 Azure 帐户，可以[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 建议使用 RAM 至少为 1 GB 的 VM。 [详细了解](../virtual-machines/windows/quick-create-portal.md)如何创建 VM。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="enable-replication-for-the-azure-vm"></a>为 Azure VM 启用复制

执行以下步骤可将 VM 复制到次要位置。

1. 在 Azure 门户上，从“主页”   >   “虚拟机”菜单中选择要复制的 VM。
1. 在“操作”中，选择“灾难恢复”   。
1. 从“基本信息”   >   “目标区域”中，选择目标区域。
1. 若要查看复制设置，请选择“查看 + 开始复制”  。 如需更改任何默认设置，请选择“高级设置”  。
1. 若要启动启用 VM 复制的作业，请选择“启动复制”  。

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="启用复制。":::

## <a name="verify-settings"></a>验证设置

复制作业完成后，可以检查复制状态、修改复制设置和测试部署。

1. 在 Azure 门户菜单中选择“虚拟机”  ，然后选择已复制的 VM。
1. 在“操作”中，选择“灾难恢复”   。
1. 若要查看“概览”中的复制详细信息，  请选择“基本信息”  。 更多详细信息显示在“运行状况和状态”  、“故障转移就绪情况”和“基础结构视图”映射中。  

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="复制状态。":::

## <a name="clean-up-resources"></a>清理资源

若要停止对主要区域中 VM 的复制，必须禁用复制：

- 将自动清除源复制设置。
- 在复制过程中安装在 VM 上的 Site Recovery 扩展不会被删除。
- 对 VM 的 Site Recovery 计费会停止。

若要禁用复制，请执行以下步骤：

1. 在 Azure 门户菜单中选择“虚拟机”  ，然后选择已复制的 VM。
1. 在“操作”中，选择“灾难恢复”   。
1. 从“概览”中选择“禁用复制”  。 
1. 若要卸载 Site Recovery 扩展，请转到 VM 的“设置” > “扩展”。  

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="禁用复制。":::

## <a name="next-steps"></a>后续步骤

在本快速入门中，将单个 VM 复制到了次要区域。 接下来，为多个 Azure VM 设置复制。

> [!div class="nextstepaction"]
> [为 Azure VM 设置灾难恢复](azure-to-azure-tutorial-enable-replication.md)

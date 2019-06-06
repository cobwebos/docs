---
title: 为 Azure IaaS VM 设置到 Azure 次要区域的灾难恢复
description: 本快速入门提供了使用 Azure Site Recovery 服务在 Azure 区域之间对 Azure IaaS VM 进行灾难恢复所需的步骤。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 05/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 46376091649a2c2b1498fd13e8775fb161de32c1
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399543"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>为 Azure VM 设置到 Azure 次要区域的灾难恢复        

[Azure Site Recovery](site-recovery-overview.md) 服务通过在计划内和计划外中断期间使商业应用程序保持启动和运行状态，有助于实施业务连续性和灾难恢复 (BCDR) 策略。 Site Recovery 管理并安排本地计算机和 Azure 虚拟机 (VM) 的灾难恢复，包括复制、故障转移和恢复。

本快速入门介绍了如何通过将 Azure VM 复制到不同的 Azure 区域来为其设置灾难恢复。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 本文是针对新用户的快速演练。 它使用带有默认选项和最小自定义的最简单路径。  有关更完整的演练，请查看[我们的教程](azure-to-azure-tutorial-enable-replication.md)。

## <a name="log-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="enable-replication-for-the-azure-vm"></a>为 Azure VM 启用复制

1. 在 Azure 门户中，单击“虚拟机”  ，并选择要复制的 VM。
2. 在“操作”  中，单击“灾难恢复”  。
3. 在“配置灾难恢复”   > “目标区域”  中，选择要复制到的目标区域。
4. 在本快速入门中，接受其他默认设置。
5. 单击“启用复制”。  这将启动用于为 VM 启用复制的作业。

    ![启用复制](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>验证设置

复制作业完成后，可以检查复制状态、修改复制设置和测试部署。

1. 在“操作”  中，单击“灾难恢复”  。
2. 可以验证复制运行状况、已创建的恢复点以及映射中的源和目标区域。

   ![复制状态](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>清理资源

对主要区域中的 VM 禁用复制时，该 VM 会停止复制：

- 将自动清除源复制设置。 在复制过程中安装在 VM 上的 Site Recovery 扩展不会被删除，必须手动删除。 
- 对 VM 的 Site Recovery 计费会停止。

按如下所述停止复制

1. 选择 VM。
2. 在“灾难恢复”  中，单击“禁用复制”  。

   ![禁用复制](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>后续步骤

在本快速入门中，将单个 VM 复制到了次要区域。 现在，请尝试使用恢复计划复制多个 Azure VM。

> [!div class="nextstepaction"]
> [为 Azure VM 配置灾难恢复](azure-to-azure-tutorial-enable-replication.md)

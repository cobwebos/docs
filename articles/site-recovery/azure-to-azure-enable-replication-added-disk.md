---
title: 在 Azure Site Recovery 中为添加的 Azure VM 磁盘启用复制
description: 本文介绍如何为已添加到 Azure VM 的磁盘启用复制，该磁盘已为启用了灾难恢复的 Azure VM 提供 Azure Site Recovery
services: site-recovery
author: carmonmills
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: carmonm
ms.openlocfilehash: 08ab539a1b05c67ef77c84389dad7247b72355d6
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75929894"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>为添加到 Azure VM 的磁盘启用复制


本文介绍如何使用[Azure Site Recovery](site-recovery-overview.md)为已在 azure VM 中添加到已启用灾难恢复的 azure VM 的数据磁盘进行复制。

对于包含托管磁盘的 Azure Vm，支持向 VM 中添加的磁盘启用复制。

将新磁盘添加到复制到其他 Azure 区域的 Azure VM 时，将发生以下情况：

-   VM 的复制运行状况显示一条警告，并在门户中通知你有一个或多个磁盘可用于保护。
-   如果为添加的磁盘启用保护，则在磁盘初始复制后，将会出现警告。
-   如果选择不启用磁盘复制，则可以选择关闭该警告。

![已添加新磁盘](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>开始之前

本文假设你已为要向其添加磁盘的 VM 设置了灾难恢复。 如果尚未执行此操作，请遵循[azure 到 azure 灾难恢复教程](azure-to-azure-tutorial-enable-replication.md)。 

## <a name="enable-replication-for-an-added-disk"></a>为添加的磁盘启用复制 

若要为添加的磁盘启用复制，请执行以下操作：

1. 在保管库中 > "**复制的项**"，单击要向其添加磁盘的 VM。
2. 单击 "**磁盘**"，然后选择要为其启用复制的数据磁盘（这些磁盘的状态为 "**不受保护**"）。
3.  在 "**磁盘详细信息**" 中，单击 "**启用复制**"。

    ![为添加的磁盘启用复制](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

运行 "启用复制" 作业并完成初始复制后，将删除磁盘问题的复制运行状况警告。



## <a name="next-steps"></a>后续步骤

[详细了解](site-recovery-test-failover-to-azure.md)如何运行测试故障转移。

---
title: 为磁盘添加到由 Azure Site Recovery 复制 Azure VM 启用复制 |Microsoft Docs
description: 本文介绍如何为添加到 Azure VM 的磁盘启用复制，该 Azure VM 已使用 Azure Site Recovery 启用灾难恢复
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: asgang
ms.openlocfilehash: 4a262a3a0c32516988890a6afc6eef34d8655c89
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671875"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>为添加到 Azure VM 的磁盘启用复制


本文介绍如何为添加到 Azure VM 的数据磁盘启用复制，该 Azure VM 已使用 [Azure Site Recovery](site-recovery-overview.md) 启用到另一个 Azure 区域的灾难恢复。

对于具有托管磁盘的 Azure VM，支持为添加到 VM 的磁盘启用复制。

将新磁盘添加到正在复制到另一个 Azure 区域的 Azure VM 时，会发生以下情况：

-   VM 的复制运行状况会显示一条警告，并且门户中的注意事项会通知你有一个或多个磁盘可用于保护。
-   如果为添加的磁盘启用保护，此警告会在初始复制磁盘后消失。
-   如果选择不为磁盘启用复制，则可选择关闭此警告。

![添加的新磁盘](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>开始之前

本文假定你已经为要添加磁盘的 VM 设置了灾难恢复。 如果你还没有这样做，请按照 [Azure 到 Azure 灾难恢复教程](azure-to-azure-tutorial-enable-replication.md)进行操作。 

## <a name="enable-replication-for-an-added-disk"></a>为添加的磁盘启用复制 

若要为添加的磁盘启用复制，请执行以下操作：

1. 在保管库 >“复制的项”  中，单击已向其添加磁盘的 VM。
2. 单击“磁盘”  ，然后选择要为其启用复制的数据磁盘（这些磁盘具有“未受保护”  状态）。
3.  在“磁盘详细信息”  中，单击“启用复制”  。

    ![为添加的磁盘启用复制](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

在启用复制作业运行且初始复制完成后，将删除针对磁盘问题的复制运行状况警告。



# <a name="next-steps"></a>后续步骤

[详细了解](site-recovery-test-failover-to-azure.md)如何运行测试故障转移。

---
title: 为磁盘添加到由 Azure Site Recovery 复制 Azure VM 启用复制 |Microsoft Docs
description: 本文介绍如何为磁盘添加到启用了使用 Azure Site Recovery 的灾难恢复 Azure VM 启用复制
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 69122ffe9cefa3e1b9c6c8fbadfa80492ebebbde
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928057"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>为磁盘添加到 Azure VM 启用复制


本文介绍如何为数据磁盘添加到已启用用于灾难恢复到另一个 Azure 区域的 Azure VM 启用复制使用[Azure Site Recovery](site-recovery-overview.md)。

启用向 VM 添加磁盘的复制支持对 Azure Vm 使用托管磁盘。

在将新磁盘添加到 Azure VM 复制到另一个 Azure 区域中时，发生以下情况：

-   VM 复制运行状况显示一条警告，并注意门户中的通知一个或更多磁盘可用于保护。
-   如果启用对所添加的磁盘的保护，该警告将消失后的初始复制的磁盘。
-   如果您选择不启用复制的磁盘，可以选择取消显示此警告。

![添加新磁盘](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>开始之前

本文假定已设置灾难恢复设置为要将磁盘添加到 VM。 如果您没有，请按照[Azure 到 Azure 灾难恢复教程](azure-to-azure-tutorial-enable-replication.md)。 

## <a name="enable-replication-for-an-added-disk"></a>为添加的磁盘启用复制 

若要启用复制添加的磁盘，请执行以下操作：

1. 在保管库 >**复制的项**，单击该磁盘添加到 VM。
2. 单击**磁盘**，然后选择你想要启用复制的数据磁盘 (这些磁盘具有**不受保护**状态)。
3.  在中**磁盘详细信息**，单击**启用复制**。

    ![为添加的磁盘启用复制](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

启用复制作业运行和初始复制完成后，将删除此磁盘问题的复制运行状况警告。



# <a name="next-steps"></a>后续步骤

[详细了解](site-recovery-test-failover-to-azure.md)如何运行测试故障转移。

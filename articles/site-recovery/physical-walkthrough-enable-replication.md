---
title: "使用 Azure Site Recovery 将物理服务器复制到 Azure | Microsoft Docs"
description: "总结了使用 Azure Site Recovery 服务将物理服务器复制到 Azure 所需的步骤"
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 519c5559-7032-4954-b8b5-f24f5242a954
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 42f35c53eec06a346281fd90c97aecfd2269307d
ms.contentlocale: zh-cn
ms.lasthandoff: 06/29/2017

---
# <a name="step-10-enable-replication-for-physical-servers-to-azure"></a>步骤 10：将物理服务器复制到 Azure


本文介绍如何在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地 Windows/Linux 物理服务器复制到 Azure。

请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="before-you-start"></a>开始之前

- 服务器必须[安装有移动服务组件](physical-walkthrough-install-mobility.md)。
- 如果已为推送安装准备了 VM，那么在启用复制时，进程服务器将自动安装移动服务。
- 启用计算机的复制时，你的 Azure 用户帐户需要特定[权限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)来确保你能够使用 Azure 存储并创建 Azure VM。
- 添加或修改服务器的 IP 地址时，可能需要 15 分钟或更长时间，更改才会生效并显示在门户中。


## <a name="exclude-disks-from-replication"></a>从复制中排除磁盘

默认情况下将复制计算机上的所有磁盘。 你可以从复制中排除磁盘。 例如，你可能不想要复制包含临时数据，或者每当重新启动计算机或应用程序时刷新的数据（例如 pagefile.sys 或 SQL Server tempdb）的磁盘。 [了解详细信息](site-recovery-exclude-disk.md)

## <a name="replicate-servers"></a>复制服务器

1. 单击“步骤 2: 复制应用程序” > “源”。
2. 在“源”中选择配置服务器。
3. 在“计算机类型”中选择“物理计算机”。
4. 选择进程服务器。 如果尚未创建任何额外的进程服务器，该进程服务器将是配置服务器。 。
5. 在“目标”中，选择要创建故障转移 VM 的订阅和资源组。 选择要在 Azure 中用于故障转移 VM 的部署模型（经典或资源管理）。
6. 选择要用于复制数据的 Azure 存储帐户。 如果不想使用已设置的帐户，可以创建一个新帐户。
7. 选择 Azure VM 在故障转移后所要连接的 Azure 网络和子网。 选择“立即为选定的计算机配置”，将网络设置应用到选择保护的所有计算机。 选择“稍后配置”以选择每个计算机的 Azure 网络。 如果不想使用现有网络，可以创建一个网络。

    ![启用复制](./media/physical-walkthrough-enable-replication/targetsettings.png)

8. 在“物理机”中，单击“+物理机”，然后输入“名称”和“IP 地址”。 选择要复制的计算机的操作系统。 发现计算机并将其显示在列表中需要几分钟。
9. 在“属性” > “配置属性”中，选择进程服务器在计算机上自动安装移动服务时使用的帐户。
10. 默认情况下会复制所有磁盘。 单击“所有磁盘”并清除不想要复制的所有磁盘。 。 可以稍后再设置其他 VM 属性。

    ![启用复制](./media/physical-walkthrough-enable-replication/enable-replication6.png)
11. 在“复制设置” > “配置复制设置”中，检查是否选择了正确的复制策略。 如果修改策略，更改将应用到复制计算机和新计算机。
12. 如果要将计算机集合到复制组，请启用“多 VM 一致性”并指定组的名称。 。 请注意：

    * 复制组中的计算机将一起复制，并在故障转移时具有在崩溃时保持一致且应用保持一致的共享恢复点。
    * 我们建议你将物理服务器集合在一起，使其镜像你的工作负荷。 启用多 VM 一致性可能会影响工作负荷性能，因此，仅当计算机运行相同的工作负荷并且你需要一致性时，才应使用该设置。

13. 单击“启用复制”。 可以在“设置” > “作业” > “Site Recovery 作业”中，跟踪“启用保护”作业的进度。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。

## <a name="next-steps"></a>后续步骤

转到[步骤 11：运行测试故障转移](physical-walkthrough-test-failover.md)


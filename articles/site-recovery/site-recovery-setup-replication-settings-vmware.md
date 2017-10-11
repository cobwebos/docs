---
title: "设置 Azure Site Recovery 的复制设置 |Microsoft 文档"
description: "描述如何部署 Site Recovery，以安排复制、 故障转移和在 VMM 云中的 HYPER-V 虚拟机恢复到 Azure。"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/05/2017
ms.author: sutalasi
ms.openlocfilehash: 73a1f19177f23441f5f7165cf2bc92ba85e62aa5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>管理的 VMware 到 Azure 的复制策略


## <a name="create-a-replication-policy"></a>创建复制策略

1. 选择**管理** > **站点恢复基础结构**。
2. 选择**复制策略**下**为 VMware 和物理机**。
3. 选择**+ 复制策略**。

    ![创建复制策略](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. 输入策略名称。

5. 在**RPO 阈值**，指定 RPO 限制。 连续复制超出此限制时，将生成警报。
6. 在**恢复点保留期**，每个恢复点保留窗口的持续时间指定 （以小时为单位）。 受保护的计算机可以恢复到保留窗口内的任何点。

    > [!NOTE]
    > 计算机复制到高级存储被支持最多 24 小时的保留。 计算机复制到标准存储被支持最多 72 个小时的保留期。

    > [!NOTE]
    > 自动创建故障回复复制策略。

7. 在**应用一致快照频率**，指定频率 （以分钟为单位） 将创建包含应用程序一致的快照的恢复点。

8. 单击" **确定**"。 应在 30 到 60 秒内创建策略。

![复制策略生成情况](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-a-configuration-server-with-a-replication-policy"></a>将配置服务器与复制策略相关联
1. 选择你想要将配置服务器关联的复制策略。
2. 单击**关联**。
![相关联的配置服务器](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. 从服务器的列表中选择配置服务器。
4. 单击" **确定**"。 应在一到两分钟内关联配置服务器。

![配置服务器关联](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-a-replication-policy"></a>编辑复制策略
1. 选择你想要编辑的复制设置的复制策略。
![编辑复制策略](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. 单击**编辑设置**。
![编辑复制策略设置](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. 更改基于您的需要的设置。
4. 单击 **“保存”**。 策略应保存在 2 到 5 分钟，具体取决于多少台 Vm 正在使用该复制策略。

![保存复制策略](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-a-configuration-server-from-a-replication-policy"></a>取消关联的复制策略从配置服务器
1. 选择你想要将配置服务器关联的复制策略。
2. 单击**取消关联**。
3. 从服务器的列表中选择配置服务器。
4. 单击" **确定**"。 配置服务器应在一到两分钟内解除关联。

    > [!NOTE]
    > 如果没有至少一个复制的项目中使用的策略，无法取消关联的配置服务器。 请确保没有复制的项之前取消关联配置服务器使用的策略。

## <a name="delete-a-replication-policy"></a>删除复制策略

1. 选择你想要删除的复制策略。
2. 单击**删除**。 应在 30 到 60 秒内删除的策略。

    > [!NOTE]
    > 如果它具有与之关联的至少一个配置服务器，无法删除复制策略。 请确保没有复制的项使用的策略，并删除所有关联的配置服务器，然后删除策略。

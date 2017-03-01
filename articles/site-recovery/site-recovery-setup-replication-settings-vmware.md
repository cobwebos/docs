---
title: "为 Azure Site Recovery 设置复制设置 | Microsoft 文档"
description: "介绍了如何部署 Site Recovery，以用于协调 VMM 云中 Hyper-V VM 到 Azure 的复制、故障转移和恢复。"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/19/2017
ms.author: sutalasi
translationtype: Human Translation
ms.sourcegitcommit: 74d51269cdd55e39eaa2963fec2b409decb47d0a
ms.openlocfilehash: a279fdc35c62c76e6f0858d52c64240032669f15


---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>管理从 VMware 复制到 Azure 的复制策略


## <a name="create-a-new-replication-policy"></a>创建新的复制策略

1. 在左侧菜单上单击“管理 -> 站点恢复基础结构”。 
2. 在“适用于 VMware 机和物理机”部分选择“复制策略”。
3. 单击顶部的“+复制策略”。

    ![创建复制策略](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. 输入策略名称。

5. 在“RPO 阈值”中，指定 RPO 限制。 当连续复制超出此限制时，将生成警报。
6. 在“恢复点保留期”中，针对每个恢复点指定保留期的时长（以小时为单位）。 受保护的计算机可以恢复到某个时段内的任意时间点。 

    > [!NOTE] 
    > 复制到高级存储的计算机最多允许保留 24 小时，复制到标准存储的计算机最多允许保留 72 小时。
    
    > [!NOTE] 
    > 将会自动创建故障回复复制策略。

7. 在“应用一致性快照频率”中，指定创建包含应用程序一致性快照的恢复点的频率（以分钟为单位）。

8. 单击“确定”。 策略应在大约 30 秒到 1 分钟之内创建。

![创建复制策略](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-configuration-server-with-replication-policy"></a>将配置服务器与复制策略关联
1. 单击要与配置服务器关联的复制策略。
2. 单击顶部的“关联”。
![创建复制策略](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. 从服务器列表中选择“配置服务器”。
4. 单击“确定”。 配置服务器应在大约 1 到 2 分钟内完成关联。

![创建复制策略](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-replication-policy"></a>编辑复制策略
1. 单击要编辑其复制设置的复制策略。
![创建复制策略](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. 单击顶部的“编辑设置”。
![创建复制策略](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. 根据需要更改设置。
4. 单击顶部的“保存”。 策略应在大约 2 到 5 分钟内保存完毕，具体取决于有多少 VM 正在使用该复制策略。

![创建复制策略](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-configuration-server-from-replication-policy"></a>取消配置服务器与复制策略的关联
1. 单击要与配置服务器关联的复制策略。
2. 单击顶部的“取消关联”。
3. 从服务器列表中选择“配置服务器”。
4. 单击“确定”。 配置服务器应在大约 1 到 2 分钟内取消关联。
    
    > [!NOTE] 
    > 如果至少有一个复制的项正在使用策略，则不能取消配置服务器的关联。 请确保没有复制的项正在使用策略，然后再取消配置服务器的关联。

## <a name="delete-replication-policy"></a>删除复制策略 

1. 单击要删除的复制策略。
2. 单击“删除”。 策略应在大约 30 秒到 1 分钟之内删除。

    > [!NOTE] 
    > 如果至少有 1 个配置服务器与复制策略关联，则不能删除该复制策略。 请确保没有复制的项正在使用策略并删除所有关联的配置服务器，然后再删除策略。


<!--HONumber=Jan17_HO4-->



---
title: 为 Azure Site Recovery 设置复制设置 | Microsoft 文档
description: 介绍了如何部署 Site Recovery，以协调 VMM 云中 Hyper-V VM 到 Azure 的复制、故障转移和恢复。
services: site-recovery
documentationcenter: ''
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/22/2018
ms.author: sutalasi
ms.openlocfilehash: 0a567f31bf1991d4c2a95468d2abc31c51a878f3
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2018
ms.locfileid: "29767418"
---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>管理从 VMware 复制到 Azure 的复制策略


## <a name="create-a-replication-policy"></a>创建复制策略

1. 选择“管理” > “Site Recovery 基础结构”。
2. 在“适用于 VMware 和物理机”中选择“复制策略”。
3. 选择“+ 复制策略”。

    ![创建复制策略](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. 输入策略名称。

5. 在“RPO 阈值”中：指定 RPO 限制。 当连续复制超出此限制时，将生成警报。
6. 在“恢复点保留期”中，针对每个恢复点指定保留期的时长（以小时为单位）。 受保护的计算机可以恢复到某个保留期时段内的任意时间点。

    > [!NOTE]
    > 复制到高级存储的计算机最长支持 24 小时的保留期。 复制到标准存储的计算机最长支持 72 小时的保留期。

    > [!NOTE]
    > 会自动创建故障回复的复制策略。

7. 在“应用一致性快照频率”中，指定创建包含应用程序一致性快照的恢复点的频率（以分钟为单位）。

8. 单击“确定”。 应会在 30 秒到 1 分钟之内创建好策略。

![复制策略生成](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-a-configuration-server-with-a-replication-policy"></a>将配置服务器与复制策略关联
1. 选择要与配置服务器关联的复制策略。
2. 单击“关联”。
![关联配置服务器](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. 从服务器列表中选择配置服务器。
4. 单击“确定”。 应会在 1 到 2 分钟内关联配置服务器。

![配置服务器关联](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-a-replication-policy"></a>编辑复制策略
1. 选择要编辑其复制设置的复制策略。
![编辑复制策略](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. 单击“编辑设置”。
![编辑复制策略设置](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. 根据需要更改设置。
4. 单击“ **保存**”。 应在 2 到 5 分钟内保存策略，具体取决于正在使用该复制策略的 VM 数量。

![保存复制策略](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-a-configuration-server-from-a-replication-policy"></a>从复制策略取消关联配置服务器
1. 选择要与配置服务器关联的复制策略。
2. 单击“取消关联”。
3. 从服务器列表中选择配置服务器。
4. 单击“确定”。 应会在 1 到 2 分钟内取消关联配置服务器。

    > [!NOTE]
    > 如果至少有一个复制项正在使用该策略，则无法取消关联配置服务器。 在取消关联配置服务器前，请确保没有复制项正在使用该策略。

## <a name="delete-a-replication-policy"></a>删除复制策略

1. 选择要删除的复制策略。
2. 单击“删除” 。 应会在 30 秒到 1 分钟之内删除策略。

    > [!NOTE]
    > 如果至少有 1 台配置服务器与某个复制策略关联，则无法删除该策略。 在删除策略前，请确保没有复制项正在使用该策略，并删除所有关联的配置服务器。

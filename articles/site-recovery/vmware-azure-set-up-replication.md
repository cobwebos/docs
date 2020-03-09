---
title: 为 VMware 灾难恢复设置复制策略 Azure Site Recovery |Microsoft Docs
description: 介绍如何配置复制设置，以便使用 Azure Site Recovery 从 VMware 灾难恢复到 Azure。
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362529"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>为 VMware 灾难恢复配置和管理复制策略

本文介绍如何配置复制策略，以便使用 [Azure Site Recovery](site-recovery-overview.md) 将 VMware VM 复制到 Azure。

## <a name="create-a-policy"></a>创建策略

1. 选择“管理” > “Site Recovery 基础结构”。
2. 在“适用于 VMware 和物理计算机”中选择“复制策略”。
3. 单击“+复制策略”，并指定策略名称。
4. 在“RPO 阈值”中：指定 RPO 限制。 当连续复制超出此限制时，将生成警报。
5. 在“恢复点保留期”中，针对每个恢复点指定保留期的时长（以小时为单位）。 受保护的计算机可以恢复到某个保留期时段内的任意时间点。 复制到高级存储的计算机最长支持 24 小时的保留期。 标准存储最长支持 72 个小时。
6. 在 "**应用一致性快照频率**" 中，从下拉列表中选择应创建包含应用程序一致性快照的恢复点的频率（以小时为单位）。 如果要关闭应用程序一致性点的生成，请在下拉列表中选择 "关闭" 值。
7. 单击“确定”。 应会在 30 秒到 1 分钟之内创建好策略。

创建复制策略时，会使用后缀“failback”自动创建相应的故障回复复制策略。 创建策略后，可以通过选择它 >“编辑设置”对其进行编辑。

## <a name="associate-a-configuration-server"></a>关联配置服务器

将复制策略与本地配置服务器相关联。

1. 单击“关联”，并选择配置服务器。

    ![关联配置服务器](./media/vmware-azure-set-up-replication/associate1.png)
2. 单击“确定”。 应会在 1 到 2 分钟内关联配置服务器。

    ![配置服务器关联](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>编辑策略

创建复制策略后，可以对其进行修改。

- 策略中的更改将应用到使用策略的所有计算机。
- 如果要将复制的计算机与不同的复制策略相关联，则需要禁用和重新启用对相关计算机的保护。

编辑策略，如下所示：
1. 选择 "管理" "**管理**" > **Site Recovery 基础结构** > **复制策略**"。
2. 选择要修改的复制策略。
3. 单击 "**编辑设置**"，并根据需要更新 "RPO 阈值/恢复点保留时间"/"应用一致性快照频率" 字段。
4. 如果要关闭应用程序一致性点的生成，请在 "**应用一致性快照频率**" 字段的下拉列表中选择 "关" 值。
5. 单击“ **保存**”。 策略应在30到60秒后更新。



## <a name="disassociate-or-delete-a-replication-policy"></a>取消关联或删除复制策略

1. 选择复制策略。
    a. 若要将策略与配置服务器取消关联，请确保没有复制计算机在使用该策略。 然后，单击“取消关联”。
    b. 若要删除策略，请确保它未与配置服务器关联。 然后单击“删除”。 应需要 30-60 秒才能删除。
2. 单击“确定”。

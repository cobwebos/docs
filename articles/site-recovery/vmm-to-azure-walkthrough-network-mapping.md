---
title: "配置网络映射，以使用 Azure Site Recovery 将 VMM 云中的 HYPER-V VM 复制到 Azure | Microsoft Docs"
description: "介绍在使用 Azure Site Recovery 将 VMM 云中的 HYPER-V VM 复制到 Azure 时如何配置网络映射"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: ed6f73d8baea5af0d2aa5f0ae885f305911ccc82
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="step-9-configure-network-mapping-for-hyper-v-replication-with-vmm-to-azure"></a>步骤 9：配置 Hyper-V 到 Azure 复制（包含 VMM）的网络映射

指定[源和目标复制设置](vmm-to-azure-walkthrough-source-target.md)之后，请使用本文配置网络映射，在本地 VMM VM 网络和 Azure 网络之间进行映射。

请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="before-you-start"></a>开始之前

- 了解[网络映射](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure)。
- [准备网络映射的 VMM](vmm-to-azure-walkthrough-network.md#prepare-vmm-for-network-mapping)。 
- 确认 VMM 服务器上的虚拟机已连接到 VM 网络，并且至少创建了一个 Azure 虚拟网络。 可以将多个 VM 网络映射到单个 Azure 网络。

## <a name="configure-mapping"></a>配置映射

按如下所述配置映射：

1. 在“Site Recovery 基础结构” > “网络映射” > “网络映射”中，单击“+网络映射”图标。

    ![网络映射](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping1.png)
2. 在“添加网络映射”中，选择源 VMM 服务器，并选择“Azure”作为目标。
3. 在故障转移后，验证订阅和部署模型。
4. 在“源网络”中，从 VMM 服务器的关联列表中选择要映射的源本地 VM 网络。
5. 在“目标网络”中，选择副本 Azure VM 创建后所在的 Azure 网络。 然后单击“确定”。

    ![网络映射](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping2.png)

下面是网络映射开始时发生的事情：

* 映射开始时，源 VM 网络上的现有 VM 将连接到目标网络。 进行复制时，连接到源 VM 网络的新 VM 将连接到映射的 Azure 网络。
* 如果修改现有的网络映射，则副本虚拟机将使用新设置进行连接。
* 如果目标网络具有多个子网，并且其中一个子网与源虚拟机所在的子网同名，则在故障转移后副本虚拟机将连接到该目标子网。
* 如果没有具有匹配名称的目标子网，则虚拟机将连接到网络中的第一个子网。



## <a name="next-steps"></a>后续步骤

转到[步骤 10：创建复制策略](vmm-to-azure-walkthrough-replication.md)

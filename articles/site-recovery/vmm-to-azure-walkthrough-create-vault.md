---
title: "设置保管库以使用 Azure Site Recovery 为将 Hyper-V（包含 System Center VMM）复制到 Azure | Microsoft Docs"
description: "总结设置保管库以使用 Azure Site Recovery 将 Hyper-V（包含 VMM）复制到 Azure 所需的步骤"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: b3cd6f03-c33c-406d-91d4-5cba69f79abd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: af453ec27ba15ad8c59cf9f544584ad18dc0f74a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="step-7-set-up-a-vault-for-hyper-v-replication"></a>步骤 7：为 Hyper-V 复制设置保管库

本文介绍如何在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务设置保管库并指定希望从本地位置复制到 Azure 的内容。


请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]



## <a name="select-a-protection-goal"></a>选择保护目标

选择要复制的内容以及要复制到的位置。

1. 单击“恢复服务保管库”> 保管库。
2. 在“资源”菜单中，单击“Site Recovery” > “准备基础结构” > “保护目标”。
3. 在“保护目标”中，选择“到 Azure” > “是，使用 Hyper-V”。 选择“是”，确认正在使用 VMM。 

     ![选择目标](./media/vmm-to-azure-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>后续步骤

转到[第 8 步：设置源和目标](vmm-to-azure-walkthrough-source-target.md)

---
title: "使用 Azure Site Recovery 为将 Hyper-V（不含 System Center VMM）复制到 Azure 设置保管库 | Microsoft Docs"
description: "总结使用 Azure Site Recovery 为将 Hyper-V 复制到 Azure 设置保管库所需的步骤"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a936b3e2-0dbe-47ac-a98e-5285d96dc786
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: 8212ff011633c3a89d3310e828b6d5f1cda6ce3f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
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
3. 在“保护目标”中，选择“到 Azure” > “是，使用 Hyper-V”。 选择“否”，确认未使用 VMM。 

    ![选择目标](./media/hyper-v-site-walkthrough-create-vault/choose-goals2.png)



## <a name="next-steps"></a>后续步骤

转到[第 8 步：设置源和目标](hyper-v-site-walkthrough-source-target.md)

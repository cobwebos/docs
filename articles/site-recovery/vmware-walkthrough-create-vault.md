---
title: "创建保管库以便使用 Azure Site Recovery 将 VMware VM 复制到 Azure | Microsoft Docs"
description: "总结了创建保管库以便使用 Azure Site Recovery 将 VMware VM 复制到 Azure 的必要步骤"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 8bce940e-f19f-4418-8360-aee7b073519a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: dca95ad46b8de587140c3573ba6ed5702a122032
ms.contentlocale: zh-cn
ms.lasthandoff: 06/29/2017

---
# <a name="step-7-set-up-a-vault-for-vmware-replication-to-azure"></a>第 7 步：创建保管库以便将 VMware VM 复制到 Azure


本文介绍了如何创建保管库，并指定要从本地位置复制的内容，以便在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务将内容复制到 Azure。


请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。




## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>选择保护目标

选择要复制的内容以及要复制到的位置。

1. 单击“恢复服务保管库”> 保管库。
2. 在“资源”菜单中，依次单击“Site Recovery” > “准备基础结构” > “保护目标”。
3. 在“保护目标”中选择“到 Azure” > “是，使用 VMware vSphere 虚拟机监控程序”。



## <a name="next-steps"></a>后续步骤

转到[第 8 步：设置源和目标](vmware-walkthrough-source-target.md)


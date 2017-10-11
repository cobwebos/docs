---
title: "使用 Azure Site Recovery 为区域之间的 Azure VM 复制设置保管库 | Microsoft Docs"
description: "总结使用 Azure Site Recovery 为 Azure 区域之间的 Azure 复制设置保管库所需的步骤"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 40472189-3d80-4963-b175-8bddcbc2f61f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: e03d17992ee0b12049636e40188950bcc4a6f31e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="step-4-set-up-a-vault-for-azure-to-azure-replication"></a>步骤 4：为 Azure 到 Azure 的复制设置保管库

[规划网络](azure-to-azure-walkthrough-network.md)后，使用本文在 Azure 门户中通过 [Azure Site Recovery](site-recovery-overview.md) 服务为复制到另一个 Azure 区域的 Azure 虚拟机 (VM) 设置保管库。

- 完成本文后，应已设置恢复服务保管库。
- 请将任何评论发布到本文底部，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提问。



>[!NOTE]
>
> Azure VM 复制目前处于预览状态。




## <a name="create-a-vault"></a>创建保管库

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> 我们建议在要进行 VM 复制的位置创建恢复服务保管库。 例如，如果目标位置是美国中部，则在美国中部创建保管库。


## <a name="next-steps"></a>后续步骤

转到[步骤 5：启用复制](azure-to-azure-walkthrough-enable-replication.md)

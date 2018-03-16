---
title: "关于 Azure Site Recovery 中的迁移 | Microsoft Docs"
description: "本文介绍如何使用 Azure Site Recovery 服务迁移本地和 Azure VM。"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: edf6ffe1cd55884f1c18498213df290cb19bb246
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="about-migration"></a>关于迁移

阅读此文快速了解 [Azure Site Recovery](site-recovery-overview.md) 服务如何帮助迁移计算机。 

下面是可以使用 Site Recovery 迁移的内容：

- **从本地迁移到 Azure**：将本地 Hyper-V VM、VMware VM 和物理服务器迁移到 Azure。 迁移之后，在本地计算机上运行的工作负荷将在 Azure VM 上运行。 
- **在 Azure 中迁移**：在 Azure 区域之间迁移 Azure VM。 
- **迁移 AWS**：将 AWS Windows 实例迁移到 Azure IaaS VM。 


## <a name="what-do-we-mean-by-migration"></a>迁移的意思是什么？

除了可以使用 Site Recovery 对本地和 Azure VM 进行灾难恢复外，还可以使用 Site Recovery 服务迁移这些 VM。 区别是什么？

- 对于灾难恢复，定期将计算机复制到 Azure。 发生服务中断时，将从主站点将计算机故障转移到辅助 Azure 站点，并从该处对其进行访问。 当主站点再次可用时，可从 Azure 进行故障回复。
- 对于迁移，将本地计算机复制到 Azure 或将 Azure VM 复制到辅助区域。 然后可以将 VM 从主站点故障转移到辅助站点，并完成迁移过程。 不涉及故障回复。  


## <a name="migration-scenarios"></a>迁移方案

**方案** | **详细信息**
--- | ---
**从本地迁移到 Azure** | 可以将本地 VMware VM、Hyper-V VM 和物理服务器迁移到 Azure。 为此，完成的步骤几乎与完整灾难恢复的步骤一样。 只是不会将计算机从 Azure 故障回复到本地站点。
**在 Azure 区域之间进行迁移** | 可以将 Azure VM 从一个 Azure 区域迁移到另一个 Azure 区域。 迁移完成后，现在可以在迁移到的次要区域中为 Azure VM 配置灾难恢复。
**将 AWS 迁移到 Azure** | 可将 AWS 实例迁移到 Azure VM。 Site Recovery 将 AWS 实例视为物理服务器以实现迁移目的。 

## <a name="next-steps"></a>后续步骤

- [将本地计算机迁移到 Azure](migrate-tutorial-on-premises-azure.md)
- [将 VM 从一个 Azure 区域迁移到另一个](azure-to-azure-tutorial-migrate.md)
- [将 AWS 迁移到 Azure](migrate-tutorial-aws-azure.md)

---
title: 使用 Azure Migrate 迁移本地计算机
description: 本文总结了如何将本地计算机迁移到 Azure，并推荐 Azure Migrate。
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: e0e60ee346d20113b2ec7970390d9874522cc770
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87847306"
---
# <a name="migrate-on-premises-machines-to-azure"></a>将本地计算机迁移到 Azure

本文介绍用于将本地计算机迁移到 Azure 的选项。 

## <a name="migrate-with-azure-migrate"></a>使用 Azure Migrate 进行迁移

建议使用 [Azure Migrate](../migrate/migrate-services-overview.md) 服务将计算机迁移到 Azure。 Azure Migrate 是专门为服务器迁移而构建的。 Azure Migrate 提供了一个集中式中心，用于发现、评估本地计算机以及将本地计算机迁移到 Azure。

请访问以下链接以使用 Azure Migrate 进行迁移：

- [了解](../migrate/server-migrate-overview.md)用于 VMware VM 的迁移选项，然后使用[无代理](../migrate/tutorial-migrate-vmware.md)或[基于代理的](../migrate/tutorial-migrate-vmware-agent.md)迁移将 VM 迁移到 Azure。
- [将 Hyper-V VM 迁移到 Azure](../migrate/tutorial-migrate-hyper-v.md)。
- 将[物理服务器或其他 VM](../migrate/tutorial-migrate-physical-virtual-machines.md)（包括 [AWS 实例](../migrate/tutorial-migrate-aws-virtual-machines.md)）迁移到 Azure。

## <a name="migrate-with-site-recovery"></a>使用 Site Recovery 进行迁移
Site Recovery 应仅用于灾难恢复，而不用于迁移。

如果已在使用 Azure Site Recovery，并且要继续使用它进行迁移，请按照用于灾难恢复的相同步骤进行操作。

- VMware VM：[准备 Azure](tutorial-prepare-azure.md) 和 [VMware](vmware-azure-tutorial-prepare-on-premises.md)，开始[复制计算机](vmware-azure-tutorial.md)，[检查](tutorial-dr-drill-azure.md)一切是否正常运行，然后[运行故障转移](vmware-azure-tutorial-failover-failback.md)。
- Hyper-V VM：[准备 Azure](tutorial-prepare-azure-for-hyperv.md) 和 [Hyper-V](hyper-v-prepare-on-premises-tutorial.md)，开始[复制计算机](hyper-v-azure-tutorial.md)，[检查](tutorial-dr-drill-azure.md)一切是否正常运行，然后[运行故障转移](hyper-v-azure-failover-failback-tutorial.md)。
- 物理服务器：[按照演练](physical-azure-disaster-recovery.md)准备 Azure、准备进行灾难恢复的计算机并设置复制。

> [!NOTE]
> 运行故障转移进行灾难恢复时，最后一步是提交故障转移。 迁移本地计算机时，“提交”选项不相关。 而是选择“完成迁移”选项。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> 查看有关 Azure Migrate 的[常见问题解答](../migrate/resources-faq.md)。

  

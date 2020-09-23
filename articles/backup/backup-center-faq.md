---
title: 备份中心-常见问题
description: 本文解答了有关备份中心的常见问题
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: b10a9e73e65cf12c43ce28b429a8f12e0b960a76
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994800"
---
# <a name="backup-center---frequently-asked-questions"></a>备份中心-常见问题

## <a name="management"></a>管理

### <a name="can-backup-center-be-used-across-tenants"></a>备份中心是否可跨租户使用？

是的，如果使用 [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) 并委派了跨不同租户的订阅的委派访问权限，则可以使用备份中心作为单个窗格来管理租户之间的备份。

### <a name="can-backup-center-be-used-to-manage-both-recovery-services-vaults-and-backup-vaults"></a>备份中心是否可以用于管理恢复服务保管库和备份保管库？

是的，备份中心可以管理 [恢复服务保管库](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) 和 [备份保管库](backup-vault-overview.md)。

### <a name="is-there-a-delay-before-data-surfaces-in-backup-center"></a>备份中心中的数据表面之前是否有延迟？

备份中心旨在提供实时信息。 实体在单个保管库屏幕上显示的时间与同一实体在备份中心中显示的时间之间可能会有几秒钟的延迟。

## <a name="configuration"></a>Configuration

### <a name="do-i-need-to-configure-anything-to-see-data-in-backup-center"></a>是否需要配置任何内容以查看备份中心的数据？

不是。 备份中心已准备就绪。 但是，若要在备份中心下查看 [备份报表](https://docs.microsoft.com/azure/backup/configure-reports) ，需要为保管库配置报告。

### <a name="do-i-need-to-have-any-special-permissions-to-use-backup-center"></a>是否需要具有任何特殊权限才能使用备份中心？

此类备份中心不需要任何新权限。 只要你对所管理的资源具有适当级别的 RBAC 访问权限，就可以将备份中心用于这些资源。 例如，若要查看有关备份的信息，需要访问保管库的 **读者** 访问权限。 若要配置备份和执行其他与备份相关的操作，需要 **备份参与者** 或 **备份操作员** 角色。 了解有关 [Azure 备份的 RBAC 角色的](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)详细信息。

## <a name="pricing"></a>定价

### <a name="do-i-need-to-pay-anything-extra-to-use-backup-explorer"></a>是否需要支付任何额外的费用来使用备份资源管理器？

目前，除了备份成本) 使用备份中心外，不 (额外的费用。 但是，如果使用备份中心下的 [备份报表](https://docs.microsoft.com/azure/backup/configure-reports) ，则使用备份报表 Azure Monitor 日志会产生 [费用](https://azure.microsoft.com/pricing/details/monitor/) 。

## <a name="next-steps"></a>后续步骤

阅读其他常见问题：

* [有关恢复服务保管库的常见问题](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq)
* [有关 Azure VM 备份的常见问题](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq)

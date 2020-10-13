---
title: 如何跨区域迁移 Azure API 管理
description: 了解如何将 API 管理实例从一个区域迁移到另一个区域。
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 0eed2328aca78402c5f4691bb9b3d07d4f36472e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86250220"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>如何跨区域迁移 Azure API 管理
若要将 API 管理实例从一个 Azure 区域迁移到另一个 Azure 区域，可以使用[备份和还原](api-management-howto-disaster-recovery-backup-restore.md)功能。 应该在源和目标区域选择相同的 API 管理定价层。 

> [!NOTE]
> 在不同的云类型之间迁移时，备份和还原无效。 因此，需要[以模板形式](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)导出资源。 然后，改编目标 Azure 区域的已导出模板并重新创建资源。 

## <a name="option-1-use-a-different-api-management-instance-name"></a>选项 1：使用另一个 API 管理实例名称

1. 在目标区域创建新的 API 管理实例，所用定价层与源 API 管理实例相同。 新实例应该使用不同的名称。 
1. 将现有的 API 管理实例备份到存储帐户。
1. 将在步骤 2 中创建的备份还原到在步骤 1 的新区域中创建的新 API 管理实例。
1. 如果有一个指向源区域 API 管理实例的自定义域，请更改自定义域 CNAME，使之指向新的 API 管理实例。 


## <a name="option-2-use-the-same-api-management-instance-name"></a>选项 2：使用同一个 API 管理实例名称

> [!NOTE]
> 此选项会导致迁移过程中出现停机。

1. 将源区域中的 API 管理实例备份到存储帐户。
1. 删除源区域中的 API 管理。 
1. 在目标区域创建新的 API 管理实例，所用名称与源区域相同。
1. 将在步骤 1 中创建的备份还原到目标区域中的新 API 管理实例。  


## <a name="next-steps"></a><a name="next-steps"> </a>后续步骤
* 有关备份和还原功能的详细信息，请参阅[如何实现灾难恢复](api-management-howto-disaster-recovery-backup-restore.md)。
* 若要了解如何迁移 Azure 资源，请参阅 [Azure 跨区域迁移指南](https://github.com/Azure/Azure-Migration-Guidance)。
* [优化并节省云开支](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

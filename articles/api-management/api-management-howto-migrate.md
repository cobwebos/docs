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
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 831eacf8197e90d7ae6b752ee11898c9667b5b6a
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036539"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>如何跨区域迁移 Azure API 管理
若要将 API 管理实例从一个 Azure 区域迁移到另一个 Azure 区域, 可以使用[备份和还原](api-management-howto-disaster-recovery-backup-restore.md)功能。 应在源区域和目标区域中选择相同的 API 管理定价层。 

> [!NOTE]
> 在不同云类型之间迁移时, 备份和还原不起作用。 为此, 需要以[模板形式](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)导出资源。 然后, 改编目标 Azure 区域的导出模板, 然后重新创建资源。 

## <a name="option-1-use-a-different-api-management-instance-name"></a>选项 1：使用其他 API 管理实例名称

1. 在目标区域中, 使用与源 API 管理实例相同的定价层创建新的 API 管理实例。 新实例应具有不同的名称。 
1. 将现有 API 管理实例备份到存储帐户。
1. 将在步骤2中创建的备份还原到在步骤1中的新区域中创建的新 API 管理实例。
1. 如果有指向源区域 API 管理实例的自定义域, 请更新自定义域 CNAME, 使之指向新的 API 管理实例。 


## <a name="option-2-use-the-same-api-management-instance-name"></a>选项 2：使用相同的 API 管理实例名称

> [!NOTE]
> 此选项将导致迁移期间停机。

1. 将源区域中的 API 管理实例备份到存储帐户。
1. 在源区域中删除 API 管理。 
1. 在目标区域中创建与源区域中的实例名称相同的新 API 管理实例。
1. 将在步骤1中创建的备份还原到目标区域中的新 API 管理实例。  


## <a name="next-steps"></a>后续步骤
* 有关备份和还原功能的详细信息, 请参阅[如何实现灾难恢复](api-management-howto-disaster-recovery-backup-restore.md)。
* 有关迁移 Azure 资源的信息, 请参阅[azure 跨区域迁移指南](https://github.com/Azure/Azure-Migration-Guidance)。
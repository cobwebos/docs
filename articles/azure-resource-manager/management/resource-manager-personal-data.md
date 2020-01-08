---
title: 个人数据
description: 了解如何管理与 Azure 资源管理器操作相关联的个人数据。
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 22cfc1b6096980f3d10db404a1c4e02f2de355d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485255"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>管理与 Azure 资源管理器相关联的个人数据

若要避免公开敏感信息，请删除在部署、资源组或标记中提供的任何个人信息。 Azure 资源管理器提供相关操作，可以让你管理在部署、资源组或标记中提供的个人数据。

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>删除部署历史记录中的个人数据

进行部署时，资源管理器会在部署历史记录中保留参数值和状态消息。 这些值会一直保留到你从历史记录中删除部署。 若要查看你是否在这些值中提供了个人数据，请列出部署。 如果找到个人数据，请从历史记录中删除部署。

若要在历史记录中列出**部署**，请使用：

* [按资源组列表](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [az group deployment list](/cli/azure/group/deployment#az-group-deployment-list)

若要从历史记录中删除**部署**，请使用：

* [删除](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>在资源组名称中删除个人数据

资源组的名称会一直保留到你删除该资源组。 若要查看你是否在名称中提供了个人数据，请列出资源组。 如果找到个人数据，请[移动资源](move-resource-group-and-subscription.md)至新的资源组，然后删除名称中包含个人数据的资源组。

若要列出**资源组**，请使用：

* [列表](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az group list](/cli/azure/group#az-group-list)

若要删除**资源组**，请使用：

* [删除](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>删除标记中的个人数据

标记名称和值会一直保留到你删除或修改该标记。 若要查看你是否在标记中提供了个人数据，请列出标记。 如果找到个人数据，请删除这些标记。

若要列出**标记**，请使用：

* [列表](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az tag list](/cli/azure/tag#az-tag-list)

若要删除**标记**，请使用：

* [删除](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>后续步骤
* 有关 Azure 资源管理器的概述，请参阅[什么是资源管理器？](overview.md)
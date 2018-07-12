---
title: Azure 资源管理器个人数据 | Microsoft Docs
description: 了解如何管理与 Azure 资源管理器操作相关联的个人数据。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: tomfitz
ms.openlocfilehash: 71928be07080ed14fdcb93f33ea64d2572955b53
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38466018"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>管理与 Azure 资源管理器相关联的个人数据

若要避免公开敏感信息，请删除在部署、资源组或标记中提供的任何个人信息。 Azure 资源管理器提供相关操作，可以让你管理在部署、资源组或标记中提供的个人数据。

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-personal-data-in-deployment-history"></a>删除部署历史记录中的个人数据

进行部署时，资源管理器会在部署历史记录中保留参数值和状态消息。 这些值会一直保留到你从历史记录中删除部署。 若要查看你是否在这些值中提供了个人数据，请列出部署。 如果找到个人数据，请从历史记录中删除部署。

若要在历史记录中列出**部署**，请使用：

* [按资源组列表](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Get-AzureRmResourceGroupDeployment)
* [az group deployment list](/cli/azure/group/deployment#az-group-deployment-list)

若要从历史记录中删除**部署**，请使用：

* [删除](/rest/api/resources/deployments/delete)
* [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroupDeployment)
* [az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>在资源组名称中删除个人数据

资源组的名称会一直保留到你删除该资源组。 若要查看你是否在名称中提供了个人数据，请列出资源组。 如果找到个人数据，请[移动资源](resource-group-move-resources.md)至新的资源组，然后删除名称中包含个人数据的资源组。

若要列出**资源组**，请使用：

* [列表](/rest/api/resources/resourcegroups/list)
* [Get-AzureRmResourceGroup](/powershell/module/azurerm.resources/Get-AzureRmResourceGroup)
* [az group list](/cli/azure/group#az-group-list)

若要删除**资源组**，请使用：

* [删除](/rest/api/resources/resourcegroups/delete)
* [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>删除标记中的个人数据

标记名称和值会一直保留到你删除或修改该标记。 若要查看你是否在标记中提供了个人数据，请列出标记。 如果找到个人数据，请删除这些标记。

若要列出**标记**，请使用：

* [列表](/rest/api/resources/tags/list)
* [Get-AzureRmTag](/powershell/module/azurerm.tags/get-azurermtag)
* [az tag list](/cli/azure/tag#az-tag-list)

若要删除**标记**，请使用：

* [删除](/rest/api/resources/tags/delete)
* [Remove-AzureRmTag](/powershell/module/azurerm.tags/remove-azurermtag)
* [az tag delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>后续步骤
* 有关 Azure 资源管理器的概述，请参阅[什么是资源管理器？](resource-group-overview.md)
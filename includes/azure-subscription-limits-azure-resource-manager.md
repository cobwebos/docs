---
title: include 文件
description: include 文件
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 02/10/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b7db49d09d2292c08ce33ce86b1b7f427ef75fbc
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618482"
---
| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 每 Azure Active Directory 租户的订阅 | 不受限制。 | 不受限制。 |
| 每个订阅的[共同](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |不受限制。 |不受限制。 |
| 每个订阅的[资源组](../articles/azure-resource-manager/management/overview.md) |980 |980 |
| Azure 资源管理器 API 请求大小 |4194304字节。 |4194304字节。 |
| 每个订阅的标记<sup>1</sup> |不受限制。 |不受限制。 |
| 每个订阅的唯一标记计算<sup>1</sup> | 10,000 | 10,000 |
| 每个位置[的订阅级别部署](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 800<sup>2</sup> | 800 |

<sup>1</sup>每个订阅可以应用无限数量的标记。 每个资源或资源组的标记数限制为50。 仅当标记数为10000或更低时，资源管理器才返回订阅中[唯一标记名称和值的列表](/rest/api/resources/tags)。 当数字超过10000时，仍可以按标记查找资源。  

<sup>2</sup>如果达到了800个部署的限制，请从不再需要的历史记录中删除部署。 若要删除订阅级别部署，请使用[AzDeployment](/powershell/module/az.resources/Remove-AzDeployment)或[az deployment delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete)。

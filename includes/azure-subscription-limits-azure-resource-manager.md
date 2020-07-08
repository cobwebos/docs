---
title: include 文件
description: include 文件
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80334985"
---
| 资源 | 限制 |
| --- | --- |
| 每个 Azure Active Directory 租户的订阅数 | 不受限制。 |
| 每个订阅的[协同管理员数](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |不受限制。 |
| 每个订阅的[资源组数](../articles/azure-resource-manager/management/overview.md) |980 |
| Azure 资源管理器 API 请求大小 |4,194,304 字节。 |
| 每个订阅的标记数<sup>1</sup> |50 |
| 每个订阅的唯一标记计算数<sup>1</sup> | 10,000 |
| 每个位置的[订阅级部署数](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 800<sup>2</sup> |

<sup>1</sup>可以将最多 50 个标记直接应用于一个订阅。 但是，订阅可以包含无限数量的标记，这些标记应用于订阅中的资源组和资源。 每个资源或资源组的标记数限制为 50。 当标记数少于或等于 10,000 时，资源管理器仅返回订阅中[唯一标记名和值的列表](/rest/api/resources/tags)。 即使数目超过 10,000，也仍可按标记查找资源。  

<sup>2</sup>如果达到 800 个部署的限制，则会从历史记录中删除不再需要的部署。 若要删除订阅级别部署，请使用 [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) 或 [az deployment sub delete](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete)。

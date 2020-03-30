---
title: include 文件
description: include 文件
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334574"
---
| 资源 | 限制 |
| --- | --- |
| 每个目录的管理组 | 10,000 |
| 每个管理组的订阅 | 不受限制。 |
| 管理组层次结构级别 | 根级别加上 6 级<sup>1</sup> |
| 每个管理组的直接父管理组 | 一种 |
| 每个位置[的管理组级别部署](../articles/azure-resource-manager/templates/deploy-to-management-group.md) | 800<sup>2</sup> |

<sup>1</sup>6 个级别不包括订阅级别。

<sup>2</sup>如果达到 800 个部署的限制，请从不再需要的历史记录中删除部署。 要删除管理组级别部署，请使用[删除 Az 管理组部署](/powershell/module/az.resources/Remove-AzManagementGroupDeployment)或[az 部署 mg 删除](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete)。

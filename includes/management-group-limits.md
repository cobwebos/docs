---
title: 包含文件
description: include 文件
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334574"
---
| 资源 | 限制 |
| --- | --- |
| 每个目录的管理组 | 10,000 |
| 每个管理组的订阅 | 不受限制。 |
| 管理组层次结构的级别 | 根级别加上6级<sup>1</sup> |
| 每个管理组的直接父管理组 | 一个 |
| 每个位置的[管理组级别部署](../articles/azure-resource-manager/templates/deploy-to-management-group.md) | 800<sup>2</sup> |

<sup>1</sup>6级别不包括订阅级别。

<sup>2</sup>如果达到了800个部署的限制，请从不再需要的历史记录中删除部署。 若要删除管理组级别部署，请使用[AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment)或[az deployment delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete)。

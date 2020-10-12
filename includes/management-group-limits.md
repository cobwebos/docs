---
title: include 文件
description: include 文件
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 204ca121045d1bd53fd507acc7ea9691bfeac12e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88748832"
---
| 资源 | 限制 |
| --- | --- |
| 每 Azure AD 租户的管理组 | 10,000 |
| 每个管理组的订阅数 | 不受限制。 |
| 管理组层次结构的级别数 | 根级别加上 6 个级别<sup>1</sup> |
| 每个管理组的直接父管理组数 | 一种 |
| 每个位置的[管理组级别部署数](../articles/azure-resource-manager/templates/deploy-to-management-group.md) | 800<sup>2</sup> |

<sup>1</sup>6 个级别不包括订阅级别。

<sup>2</sup>如果达到 800 个部署的限制，则会从历史记录中删除不再需要的部署。 若要删除管理组级别部署，请使用 [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) 或 [az deployment mg delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete)。

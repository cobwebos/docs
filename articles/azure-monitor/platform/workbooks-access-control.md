---
title: Azure Monitor 工作簿访问控制
description: 通过基于角色的访问控制，使用预先构建的自定义参数化工作簿简化复杂的报表
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: dc6e1d738bf255fe7baa244556bad4519979b1df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86539289"
---
# <a name="access-control"></a>访问控制

工作簿中的访问控制是指两项内容：

* 读取工作簿中数据所需的访问权限。 此访问权限由工作簿中使用的资源上的标准 [Azure 角色](../../role-based-access-control/overview.md)控制。 工作簿不指定或配置对这些资源的访问权限。 用户通常可以使用这些资源上的[监视读取者](../../role-based-access-control/built-in-roles.md#monitoring-reader)角色获取对这些资源的访问权限。

* 保存工作簿所需的访问权限

    - 保存专用 `("My")` 工作簿不需要额外的权限。 所有用户都可以保存专用工作簿，并且只有他们才能查看这些工作簿。
    - 保存共享工作簿需要资源组中的写入权限才能保存工作簿。 这些权限通常由[监视参与者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)角色指定，但也可以通过*工作簿参与者*角色进行设置。
    
## <a name="standard-roles-with-workbook-related-privileges"></a>具有工作簿相关权限的标准角色

[监视读取者](../../role-based-access-control/built-in-roles.md#monitoring-reader)包含标准 /read 权限，监视工具（包括工作簿）将使用这些权限来读取资源中的数据。

[监视参与者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)包含常规 `/write` 权限，各种监视工具使用这些权限来保存项（包括用于保存共享工作簿的 `workbooks/write` 权限）。
“工作簿参与者”向对象添加“workbooks/write”权限以保存共享的工作簿。
用户无需特殊的权限即可保存仅供自己查看的专用工作簿。

对于自定义的基于角色的访问控制：

添加 `microsoft.insights/workbooks/write` 以保存共享的工作簿。 有关更多详细信息，请参阅[工作簿参与者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)角色。

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)详细了解工作簿丰富的可视化效果选项。

---
title: Azure 监视器工作簿访问控制
description: 使用基于角色的访问控制简化复杂报告，使用预构建和自定义参数化工作簿
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658415"
---
# <a name="access-control"></a>访问控制

工作簿中的访问控制是指两件事：

* 读取工作簿中数据所需的访问权限。 此访问由工作簿中使用的资源上的标准[Azure 角色](https://docs.microsoft.com/azure/role-based-access-control/overview)控制。 工作簿不指定或配置对这些资源的访问。 用户通常使用[监视读取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)在这些资源上的角色访问这些资源。

* 保存工作簿所需的访问权限

    - 保存私有`("My")`工作簿不需要额外的权限。 所有用户都可以保存专用工作簿，只有他们才能看到这些工作簿。
    - 保存共享工作簿需要资源组中的写入权限才能保存工作簿。 这些特权通常由[监视参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)角色指定，但也可以通过*工作簿参与者*角色设置。
    
## <a name="standard-roles-with-workbook-related-privileges"></a>具有工作簿相关权限的标准角色

[监视读取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)包括监视工具（包括工作簿）用于从资源读取数据的标准/读取权限。

[监视参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)包括各种`/write`监视工具用于保存项目（包括`workbooks/write`保存共享工作簿的权限）的一般权限。
"工作簿参与者"将"工作簿/写入"权限添加到对象以保存共享工作簿。
用户无需特权即可保存只有他们才能看到的私人工作簿。

对于基于角色的自定义访问控制：

添加`microsoft.insights/workbooks/write`以保存共享的工作簿。 有关详细信息，请参阅[工作簿参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)角色。

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)了解有关工作簿的许多丰富可视化选项的详细信息。

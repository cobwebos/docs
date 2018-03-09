---
title: "Azure 堆栈中的区域管理 |Microsoft 文档"
description: "概述 Azure 堆栈中的区域管理。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: brenduns
ms.reviewer: efemmano
ms.openlocfilehash: 0a19490ce276eec9a46bc0ea4343e3449a9dc93c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="region-management-in-azure-stack"></a>Azure 堆栈中的区域管理

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure 堆栈提供区域，组成构成 Azure 堆栈基础结构的硬件资源的逻辑实体的概念。 在区域管理，你可以找到需要使用才能成功运行 Azure 堆栈的基础结构生命周期的所有资源。

一个集成系统部署 (称为*Azure 堆栈云*) 组成的单一区域。 每个 Azure 堆栈开发工具包有一个区域，名为**本地**。 如果部署第二个 Azure 堆栈集成系统，或设置在单独的硬件上的开发工具包的另一个实例，此 Azure 堆栈云是不同的区域。

## <a name="information-available-through-the-region-management-tile"></a>可通过区域管理磁贴的信息
Azure 堆栈的一组区域中提供的管理功能**区域管理**磁贴。 此磁贴可供管理员门户的默认仪表板上的 Azure 堆栈运算符。 通过此磁贴，你可以监视和更新 Azure 堆栈区域和及其组件，是特定于区域的。

 ![区域管理磁贴](media/azure-stack-manage-region/image1.png)

 如果单击区域管理磁贴中的一个区域，您可以访问以下信息：

  ![区域管理边栏选项卡上的窗格的说明](media/azure-stack-manage-region/image2.png)

1. **资源菜单**。 在这里，你可以访问特定的基础结构管理方面，以及查看和管理用户资源，如存储帐户和虚拟网络。

2. **警报**。 此磁贴列出系统级警报，并提供有关每个这些警报的详细信息。

3. **更新**。 在此磁贴，你可以查看 Azure 堆栈的基础结构、 可用的更新，以及更新历史记录的当前版本。 你还可以更新你集成的系统。

4. **资源提供程序**。 资源提供程序是用于管理运行 Azure 堆栈所需的组件所提供的用户功能的位置。 每个资源提供程序附带的管理体验。 这种体验可包括用于特定的提供程序、 度量和其他特定于资源提供程序的管理功能的警报。

5. **基础结构角色**。 基础结构角色是必需的组件以运行 Azure 堆栈。 列出报告警报的基础结构角色。 通过单击角色，你可以查看与特定的角色和其中运行此角色的角色实例关联的警报。

## <a name="next-steps"></a>后续步骤
[监视运行状况和 Azure 堆栈中的警报](azure-stack-monitor-health.md)

[管理 Azure 堆栈中的更新](azure-stack-updates.md)

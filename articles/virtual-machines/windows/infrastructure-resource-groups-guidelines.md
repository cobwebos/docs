---
title: "Azure 中 Windows VM 的资源组 | Microsoft 文档"
description: "了解用于在 Azure 基础结构服务中部署资源组的关键设计和实施准则。"
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0fbcabcd-e96d-4d71-a526-431984887451
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: ce4c94a2c8983862a311d296e1a95ecabb4c9f61
ms.lasthandoff: 03/31/2017


---
# <a name="azure-resource-group-guidelines-for-windows-vms"></a>适用于 Windows VM 的 Azure 资源组准则

[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

本文重点介绍如何以逻辑方式构建环境并将所有组件组合到资源组中。

## <a name="implementation-guidelines-for-resource-groups"></a>资源组的实施准则
决策：

* 你是要以核心基础结构组件还是以完整的应用程序部署构建资源组？
* 是否需要使用基于角色的访问控制来限制对资源组的访问？

任务：

* 定义所需的核心基础结构组件和专用资源组。
* 查看如何实现 Resource Manager 模板以进行一致且可重现的部署。
* 定义控制资源组访问所需的用户访问角色。
* 使用你的命名约定来创建资源组集。 你可以使用 Azure PowerShell 或门户。

## <a name="resource-groups"></a>资源组
在 Azure 中，可以采用逻辑方式将相关资源（例如存储帐户、虚拟网络及虚拟机 (VM)）组合到一起，将它们作为单个实体进行部署、管理和维护。 从管理的角度来看，此方法可让用户在将所有相关资源放在一起的情况下更轻松地部署应用程序，或者授予他人访问该资源组的权限。 若要更全面地了解资源组，请阅读 [Azure Resource Manager 概述](../../azure-resource-manager/resource-group-overview.md)。

资源组的一项重要特征是能够使用模板构建环境。 模板就是一个 JSON 文件，用于声明存储、网络和计算资源。 还可以定义要应用的任何相关自定义脚本或配置。 通过使用这些模板，可以为应用程序创建一致且可重现的部署。 此方法可以轻松构建开发环境，然后使用相同的模板创建生产部署，反之亦然。 若要更好地了解模板的使用方式，请阅读[模板演练](../../azure-resource-manager/resource-manager-template-walkthrough.md)，了解构建模板的每个步骤。

在使用资源组设计环境时，可以采用以下两种不同的方法：

* 针对每个应用程序部署的资源组，此类部署包含存储帐户、虚拟网络和子网、VM、负载均衡器等。
* 包含核心虚拟网络和子网或存储帐户的集中式资源组。 因此，应用程序所在的资源组只包含 VM、负载均衡器、网络接口等。

随着规模的扩大，为虚拟网络和子网创建集中式资源组可让用户更轻松地为混合连接选项生成跨界网络连接。 另一种方法是让每个应用程序都有自己的需要配置和维护的虚拟网络。  [基于角色的访问控制](../../active-directory/role-based-access-control-what-is.md)提供一种精细方式，控制对资源组的访问。 对于生产应用程序，可以控制能访问那些资源的用户；对于核心基础结构资源，可以限制为只有基础结构工程师可以使用它们。 应用程序所有者只能访问其资源组内的应用程序组件，而不能访问环境的核心 Azure 基础结构。 在设计环境时，请考虑需要访问资源的用户，并据此设计资源组。 

## <a name="next-steps"></a>后续步骤
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]



---
title: "磁贴库中相关的资源与链接的资源"
description: "了解 Azure 预览门户的磁贴库中显示的相关资源和链接资源。"
services: azure-portal
documentationcenter: 
author: adamabdelhamed
manager: wpickett
editor: 
ms.assetid: dba96d29-f518-49c8-bfd2-f14cecb44cbf
ms.service: azure-portal
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2015
ms.author: adamab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a20b0b2f3967f64d2248e017b6888e21be6ef26a


---
# <a name="related-and-linked-resources-in-the-tile-gallery"></a>磁贴库中相关的资源与链接的资源
使用磁贴库可以在磁贴中查找特定的资源，并将资源拖放到你当前边栏选项卡中。 你可以使用磁贴库创建跨资源的管理视图。 对于任何指定的资源，相关资源会包含与资源相同的资源组共享的所有资源，以及与资源相互链接的任何资源。

## <a name="linked-resources-in-azure-resource-manager"></a>Azure 资源管理器中的链接资源
链接是 Azure 资源管理器的一项功能。  它可以让你声明资源之间的关系，即使它们不是驻留在相同的资源组中。 链接不会影响资源的运行时、计费，以及基于角色的访问。  它只是一个可用来表示关系的机制，使磁贴库等工具可以提供丰富的管理体验。  你的工具可以使用链接 API 来检查链接，并提供自定义的关系管理体验。 

## <a name="how-do-i-link-my-resources"></a>如何链接我的资源？
当你通过门户或者通过 Azure PowerShell 或 Azure CLI 部署模板来创建资源时，就会自动为某些依赖资源创建链接。 你还可以使用[链接资源 REST API](https://msdn.microsoft.com/library/azure/mt238499.aspx) 或者通过在模板中声明关系，以编程方式链接资源。 如需使用链接资源的完整介绍，请参阅[在 Azure 资源管理器中链接资源](../resource-group-link-resources.md)。

## <a name="next-steps"></a>后续步骤
* 有关编写 Azure 资源管理器模板的简介，请参阅[创作模板](../resource-group-authoring-templates.md)。
* 若要深入了解有关在资源之间创建链接的详细信息，请参阅[在 Azure 资源管理器中链接资源](../resource-group-link-resources.md)。
* 若要了解如何通过预览门户使用资源组，请参阅[使用 Azure 预览门户管理 Azure 资源](resource-group-portal.md)。




<!--HONumber=Nov16_HO3-->



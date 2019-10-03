---
title: 有关使用 Azure 应用程序配置管理功能标志的教程 | Microsoft Docs
description: 本教程介绍如何使用 Azure 应用程序配置将功能标志与应用程序分开管理。
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: b7fbf9add67a45c0db89fc11cee5c10bc537ab63
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393572"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>教程：在 Azure 应用程序配置中管理功能标志

可将所有功能标志存储在 Azure 应用程序配置中，并从一个位置对其进行管理。 应用程序配置提供一个名为“功能管理器”的门户 UI 专为功能标志而设计。  应用程序配置还原生支持 .NET Core 功能标志数据架构。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在应用程序配置中定义和管理功能标志。
> * 从应用程序访问功能标志。

## <a name="create-feature-flags"></a>创建功能标志

应用程序配置的 Azure 门户中的“功能管理器”提供一个 UI 用于创建和管理应用程序中使用的功能标志。

若要添加新功能标志，请执行以下操作：

1. 选择“功能管理器” > “+添加”以添加功能标志。  

    ![功能标志列表](./media/azure-app-configuration-feature-flags.png)

1. 输入功能标志的唯一键名称。 需要使用名称在代码中引用标志。

1. 如果需要，请为功能标志提供说明。

1. 设置功能标志的初始状态。 此状态通常是“关”  或“开”  。 如果向功能标志添加筛选器，则“开”  状态将更改为“条件”  。

    ![创建功能标志](./media/azure-app-configuration-feature-flag-create.png)

1. 当状态为“开”时，选择“+添加筛选器”以指定任何其他条件来限定状态   。 输入内置或自定义筛选键，然后选择“+添加参数”  以将一个或多个参数与筛选器关联。 内置筛选器包括：

    | 密钥 | JSON 参数 |
    |---|---|
    | Microsoft.Percentage | {"Value":0-100 percent} |
    | Microsoft.TimeWindow | {"Start":UTC time, "End":UTC time} |

    ![功能标志筛选器](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>更新功能标志状态

若要更改功能标志的状态值，请执行以下操作：

1. 选择“功能管理器”。 

1. 在要修改的功能标志的右侧，选择省略号 ( **...** )，然后选择“编辑”  。

1. 设置功能标志的新状态。

## <a name="access-feature-flags"></a>访问功能标志

“功能管理器”创建的功能标志作为常规的键值进行存储和检索。 它们保存在特殊的命名空间前缀 `.appconfig.featureflag` 下。 若要查看基础键值，请使用配置资源管理器。 应用程序可以使用应用程序配置的配置提供程序、SDK、命令行扩展和 REST API 来检索这些值。

## <a name="next-steps"></a>后续步骤

本教程已介绍如何使用应用程序配置管理功能标志及其状态。 有关应用程序配置和 ASP.NET Core 中的功能管理支持的详细信息，请参阅以下文章：

* [在 ASP.NET Core 应用中使用功能标志](./use-feature-flags-dotnet-core.md)

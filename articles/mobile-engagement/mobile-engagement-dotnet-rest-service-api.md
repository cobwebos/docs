---
title: 使用 REST API 访问 Azure Mobile Engagement 服务 API
description: 描述如何使用 Mobile Engagement REST API 访问 Azure Mobile Engagement 服务 API
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: ''
ms.assetid: e8df4897-55ee-45df-b41e-ff187e3d9d12
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: ea7fe6a58d8173c410573e3aa978cb1975ab5da7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>使用 REST 访问 Azure Mobile Engagement 服务 API
> [!IMPORTANT]
> Azure Mobile Engagement 已在 2018 年 3 月 31 日停用。 此页将在不久之后删除。
> 

Azure Mobile Engagement 提供 [Azure Mobile Engagement REST API](https://msdn.microsoft.com/library/azure/mt683754.aspx)，以便管理设备、市场宣传/推送市场活动等。

> [!NOTE]
> Azure Mobile Engagement 服务将于 2018 年 3 月停用，且当前仅向现有客户提供。 有关详细信息，请参阅 [Mobile Engagement](https://azure.microsoft.com/services/mobile-engagement/)。

如果不想直接使用 REST API，我们还提供了 [Swagger 文件](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json)，可以与工具搭配使用来针对首选语言生成 SDK。 我们建议使用 [AutoRest](https://github.com/Azure/AutoRest) 工具从我们提供的 Swagger 文件生成 SDK。 我们以类似的方式创建了一个 .NET SDK，以允许使用一个 C# 包装与这些 API 进行交互，并且不需要执行身份验证令牌协商，也不必自动刷新。 若要了解如何使用适用于 API 的 .net SDK，请参阅[服务 API .NET SDK 示例](mobile-engagement-dotnet-sdk-service-api.md)

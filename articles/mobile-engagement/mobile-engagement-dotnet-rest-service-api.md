---
title: "使用 REST API 访问 Azure Mobile Engagement 服务 API"
description: "描述如何使用 Mobile Engagement REST API 访问 Azure Mobile Engagement 服务 API"
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: 
ms.assetid: e8df4897-55ee-45df-b41e-ff187e3d9d12
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: e381eef22b68e89d5407fb452c05eba089b98780
ms.contentlocale: zh-cn
ms.lasthandoff: 05/18/2017


---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>使用 REST 访问 Azure Mobile Engagement 服务 API
Azure Mobile Engagement 提供 [Azure Mobile Engagement REST API](https://msdn.microsoft.com/library/azure/mt683754.aspx)，让你可管理设备、市场宣传/推送市场活动等。

如果你不想直接使用 REST API，我们还提供了 [Swagger 文件](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json)，可以与工具搭配使用来针对你的首选语言生成 SDK。 我们建议使用 [AutoRest](https://github.com/Azure/AutoRest) 工具从我们提供的 Swagger 文件生成你的 SDK。 我们以类似的方式创建了一个 .NET SDK，以允许你使用一个 C# 包装与这些 API 进行交互，并且你不需要执行身份验证令牌协商，也不必自动刷新。 若要了解如何使用适用于 API 的 .net SDK，请参阅[服务 API .NET SDK 示例](mobile-engagement-dotnet-sdk-service-api.md)


---
title: 移动应用和移动服务中的客户端与服务器 SDK 版本控制 | Microsoft Docs
description: 客户端 SDK 列表，以及移动服务与 Azure 移动应用的服务器 SDK 版本兼容性
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 87c64a98d783d2604c985017fbce586ed51e5c9d
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025429"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>移动应用和移动服务中的客户端与服务器版本控制
> [!NOTE]
> Visual Studio App Center 支持端到端和集成的服务中心到移动应用开发。 开发人员可以使用**生成**、**测试**和**分发**服务来设置持续集成和交付管道。 部署应用后，开发人员可以使用**分析**和**诊断**服务监视其应用的状态和使用情况，并使用**推送**服务与用户互动。 开发人员还可以利用 **Auth** 对用户进行身份验证，利用**数据**服务在云中持久保存和同步应用数据。
> 如果希望将云服务集成到移动应用程序中，请立即注册 App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 。

Azure 移动服务的最新版本是 Azure 应用服务的**移动应用**功能。

移动应用客户端和服务器 SDK 最初基于移动服务中的 SDK，但它们彼此*不*兼容。
也就是说，必须将移动应用客户端 SDK 与移动应用服务器 SDK 配合使用，移动服务也是如此。 可以通过客户端和服务器 SDK 使用的特殊标头值 `ZUMO-API-VERSION` 来强制实施此合约。

注意：每当本文档提到*移动服务*后端时，该后端不一定要托管在移动服务中。 现在，可以将移动服务迁移到应用服务上运行，而无需更改任何代码，但服务还是使用*移动服务* SDK 版本。

## <a name="header-specification"></a>标头规范
可以在 HTTP 标头或查询字符串中指定键 `ZUMO-API-VERSION`。 值为版本字符串，格式为 **x.y.z**。

例如：

GET https://service.azurewebsites.net/tables/TodoItem

标头：ZUMO-API-VERSION:2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>选择不进行版本检查
可以将应用设置 **MS_SkipVersionCheck** 的值设置为 **true**，选择不进行版本检查。 在 web.config 中或在 Azure 门户的“应用程序设置”部分中可以指定此设置。

> [!NOTE]
> 移动服务和移动应用之间有许多行为发生了变化，尤其是在脱机同步、身份验证和推送通知方面。 应该在完成测试之后才选择不要进行版本检查，确保这些行为的更改不会影响应用功能。

## <a name="2.0.0"></a>Azure 移动应用客户端和服务器
### <a name="MobileAppsClients"></a> 移动 *应用* 客户端 SDK
版本检查从 **Azure 移动应用**以下版本的客户端 SDK 开始引入：

| 客户端平台 | Version | 版本标头值 |
| --- | --- | --- |
| 托管客户端（Windows、Xamarin） |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>移动*应用*服务器 SDK
以下服务器 SDK 版本包含版本检查：

| 服务器平台 | SDK 中 IsInRole 中的声明 | 接受的版本标头 |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>移动应用后端的行为
| ZUMO-API-VERSION | MS_SkipVersionCheck 的值 | 响应 |
| --- | --- | --- |
| x.y.z 或 Null |True |200 - 正常 |
| Null |False/未指定 |400 - 错误的请求 |
| 1.x.y |False/未指定 |400 - 错误的请求 |
| 2.0.0-2.x.y |False/未指定 |200 - 正常 |
| 3.0.0-3.x.y |False/未指定 |400 - 错误的请求 |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server

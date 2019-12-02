---
title: 客户端和服务器 SDK 版本控制
description: 客户端 Sdk 列表，以及移动服务与 Azure 移动应用的服务器 SDK 版本兼容性。
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 4d0b301dee363c2338cb13a9fc09ee17549467eb
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668840"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>移动应用和移动服务中的客户端与服务器版本控制
> [!NOTE]
> Visual Studio App Center 支持以移动应用开发为中心的端到端集成服务。 开发人员可以使用“生成”、“测试”和“分发”服务来设置“持续集成和交付”管道。 部署应用后，开发人员可以使用“分析”和“诊断”服务监视其应用的状态和使用情况，并使用“推送”服务吸引用户。 开发人员还可以利用“身份验证”对其用户进行身份验证，并使用“数据”服务在云中保留和同步应用数据。
>
> 如果希望将云服务集成到移动应用程序中，请立即注册到 [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 中。

Azure 移动服务的最新版本是 Azure 应用服务的**移动应用**功能。

移动应用客户端和服务器 SDK 最初基于移动服务中的 SDK，但它们彼此*不*兼容。
也就是说，必须将*移动应用*客户端 SDK 与*移动应用*服务器 SDK 配合使用，*移动服务*也是如此。 可以通过客户端和服务器 SDK 使用的特殊标头值 `ZUMO-API-VERSION` 来强制实施此合约。

注意：每当本文档提到*移动服务*后端时，该后端不一定要托管在移动服务中。 现在，可以将移动服务迁移到应用服务上运行，而无需更改任何代码，但服务还是使用*移动服务* SDK 版本。

## <a name="header-specification"></a>标头规范
可以在 HTTP 标头或查询字符串中指定键 `ZUMO-API-VERSION`。 值为版本字符串，格式为 **x.y.z**。

例如：

GET https://service.azurewebsites.net/tables/TodoItem

HEADERS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>选择不进行版本检查
可以将应用设置 **MS_SkipVersionCheck** 的值设置为 **true**，选择不进行版本检查。 在 web.config 中或在 Azure 门户的“应用程序设置”部分中可以指定此设置。

> [!NOTE]
> 移动服务和移动应用之间有许多行为发生了变化，尤其是在脱机同步、身份验证和推送通知方面。 应该在完成测试之后才选择不要进行版本检查，确保这些行为的更改不会影响应用功能。

## <a name="2.0.0"></a>Azure 移动应用客户端和服务器
### <a name="MobileAppsClients"></a>移动*应用*客户端 SDK
版本检查从 **Azure 移动应用**以下版本的客户端 SDK 开始引入：

| 客户端平台 | 版本 | 版本标头值 |
| --- | --- | --- |
| 托管客户端（Windows、Xamarin） |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>移动*应用*服务器 SDK
以下服务器 SDK 版本包含版本检查：

| 服务器平台 | SDK | 接受的版本标头 |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>移动应用后端的行为
| ZUMO-API-VERSION | MS_SkipVersionCheck 的值 | 响应 |
| --- | --- | --- |
| x.y.z 或 Null |正确 |200 - 正常 |
| Null |False/未指定 |400 - 错误的请求 |
| 1.x.y |False/未指定 |400 - 错误的请求 |
| 2.0.0-2.x.y |False/未指定 |200 - 正常 |
| 3.0.0-3.x.y |False/未指定 |400 - 错误的请求 |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server

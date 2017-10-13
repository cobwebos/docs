---
title: "在 Azure 应用服务移动应用中创建 Cordova 应用 | Microsoft Docs"
description: "遵循本教程开始使用 Azure 移动应用后端进行 Apache Cordova 开发"
services: app-service\mobile
documentationcenter: javascript
author: ggailey777
manager: syntaxc4
editor: 
tags: 
keywords: "cordova,javascript,移动,客户端"
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: hero-article
ms.date: 07/07/2017
ms.author: glenga
ms.openlocfilehash: b620465cdc3cfa04933dc6e70163fc32aa9a839b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-apache-cordova-app"></a>创建 Apache Cordova 应用
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概述
本教程说明如何使用 Azure 移动应用后端向 Apache Cordova 移动应用添加基于云的后端服务。  将创建一个新的移动应用后端以及一个简单的*待办事项列表* Apache Cordova 应用，此应用将应用数据存储在 Azure 中。

只有在完成本教程后，才可以学习有关使用 Azure 应用服务中的移动应用功能的所有其他 Apache Cordova 教程。

## <a name="prerequisites"></a>先决条件
若要完成本教程，需要具备以下先决条件：

* 装有 [Visual Studio Community 2017] 或更高版本的电脑。
* [用于 Apache Cordova 的 Visual Studio 工具]
* [有效的 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)。

也可以绕过 Visual Studio，直接使用 Apache Cordova 命令行。  在 Mac 计算机上学习本教程时，使用命令行相当有效。  本教程不介绍如何使用命令行编译 Apache Cordova 客户端应用程序。

## <a name="create-an-azure-mobile-app-backend"></a>创建 Azure 移动应用后端
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[观看显示类似步骤的视频](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## <a name="configure-the-server-project"></a>配置服务器项目
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>下载并运行 Apache Cordova 应用
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## <a name="next-steps"></a>后续步骤
完成本快速入门教程后，请继续学习下列教程之一：

* [将脱机数据添加](app-service-mobile-cordova-get-started-offline-data.md)到 Apache Cordova 应用。
* [身份验证添加](app-service-mobile-cordova-get-started-users.md) 到 Apache Cordova 应用。
* [推送通知添加](app-service-mobile-cordova-get-started-push.md) 到 Apache Cordova 应用。

详细了解 Azure 应用服务的重要概念。

* [脱机数据]
* [身份验证]
* [推送通知]

了解如何使用 SDK。

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2017]: http://www.visualstudio.com/
[用于 Apache Cordova 的 Visual Studio 工具]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[脱机数据]: app-service-mobile-offline-data-sync.md
[身份验证]: app-service-mobile-auth.md
[推送通知]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

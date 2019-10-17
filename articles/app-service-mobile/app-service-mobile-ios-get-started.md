---
title: 在 Azure 应用服务移动应用中创建 iOS 应用 | Microsoft Docs
description: 遵循本教程开始使用 Azure 移动应用后端以 Objective-C 或 Swift 进行 iOS 开发
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: a0b73ba248ae9a12f9ee01f591ddbbedecc25f70
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388671"
---
# <a name="create-an-ios-app"></a>创建 iOS 应用程序

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center 支持端到端和集成的服务中心到移动应用开发。 开发人员可以使用**生成**、**测试**和**分发**服务来设置持续集成和交付管道。 部署应用后，开发人员可以使用**分析**和**诊断**服务监视应用的状态和使用情况，并使用**推送**服务与用户联系。 开发人员还可以利用**Auth**来验证其用户和**数据**服务，以便在云中持久保存和同步应用程序数据。
>
> 如果希望将云服务集成到移动应用程序中，请立即注册[App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 。

## <a name="overview"></a>概述

本教程说明如何将云后端服务 [Azure 应用服务移动应用](app-service-mobile-value-prop.md)添加到 iOS 应用。 第一步是在 Azure 上创建一个新的移动后端。 然后，下载一个简单的“待办事项列表”iOS 示例应用以在 Azure 中存储数据。

若要完成本教程，需要一台 Mac 和 [一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-new-azure-mobile-app-backend"></a>创建新的 Azure 移动应用后端

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>创建数据库连接并配置客户端和服务器项目
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-ios-app"></a>运行 iOS 应用

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

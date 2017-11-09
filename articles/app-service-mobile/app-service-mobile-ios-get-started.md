---
title: "在 Azure 应用服务移动应用中创建 iOS 应用 | Microsoft Docs"
description: "遵循本教程开始使用 Azure 移动应用后端以 Objective-C 或 Swift 进行 iOS 开发"
services: app-service\mobile
documentationcenter: ios
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 36936ae66c458fcbedeec95cfa2f573a40c8af53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-ios-app"></a>创建 iOS 应用
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概述
本教程说明如何将云后端服务 [Azure 移动应用](app-service-mobile-value-prop.md)添加到 iOS 应用。 首先将创建新的移动后端。 然后，我们将使用一个简单的“待办事项列表”iOS 应用在 Azure 中存储数据。

若要完成本教程，需要一台 Mac 和 [一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)

## <a name="step-i-create-a-new-azure-mobile-app-backend"></a>步骤 I：创建新的 Azure 移动应用后端
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="step-ii-configure-the-backend-project"></a>步骤 II：配置后端项目
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="step-iii-download-and-run-the-ios-app"></a>步骤 III：下载并运行 iOS 应用
[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

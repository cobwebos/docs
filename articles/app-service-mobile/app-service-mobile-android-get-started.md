---
title: 创建 Android 应用
description: 按照本教程开始使用适用于 Android 应用开发 Azure 移动应用后端。
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: d63faf5c06e648e4bd886d6e62c35a2b451c8c44
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668582"
---
# <a name="create-an-android-app"></a>创建 Android 应用
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center 支持以移动应用开发为中心的端到端集成服务。 开发人员可以使用“生成”、“测试”和“分发”服务来设置“持续集成和交付”管道。 部署应用后，开发人员可以使用“分析”和“诊断”服务监视其应用的状态和使用情况，并使用“推送”服务吸引用户。 开发人员还可以利用“身份验证”对其用户进行身份验证，并使用“数据”服务在云中保留和同步应用数据。
>
> 如果希望将云服务集成到移动应用程序中，请立即注册到 [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 中。

## <a name="overview"></a>概述
本教程说明如何使用 Azure 移动应用后端向 Android 移动应用添加基于云的后端服务。  将创建一个新的移动应用后端以及一个简单的 *待办事项列表* Android 应用，此应用将应用数据存储在 Azure 中。

只有在完成本教程后，才可以学习有关使用 Azure 应用服务中的移动应用功能的所有其他 Android 教程。

## <a name="prerequisites"></a>必备组件
若要完成本教程，需要以下各项：

* [Android 开发人员工具](https://developer.android.com/sdk/index.html)，其中包含 Android Studio 集成开发环境和最新的 Android 平台。
* Azure 移动 Android SDK。
* [有效的 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="create-a-new-azure-mobile-app-backend"></a>创建新的 Azure 移动应用后端
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>创建数据库连接并配置客户端和服务器项目
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-android-app"></a>运行 Android 应用程序
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

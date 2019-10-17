---
title: 创建使用 Azure 移动应用的通用 Windows 平台 (UWP) | Microsoft Docs
description: 按照本教程进行操作，开始使用 C#、Visual Basic 或 JavaScript 通过 Azure 移动应用后端进行通用 Windows 平台 (UWP) 应用开发。
services: app-service\mobile
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 3ddd5ef197740315add2960971c978addb7fe62a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388568"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>通过 Azure 后端创建 Windows 应用

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center 支持端到端和集成的服务中心到移动应用开发。 开发人员可以使用**生成**、**测试**和**分发**服务来设置持续集成和交付管道。 部署应用后，开发人员可以使用**分析**和**诊断**服务监视应用的状态和使用情况，并使用**推送**服务与用户联系。 开发人员还可以利用**Auth**来验证其用户和**数据**服务，以便在云中持久保存和同步应用程序数据。
>
> 如果希望将云服务集成到移动应用程序中，请立即注册[App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 。

## <a name="overview"></a>概述

本教程说明如何向通用 Windows 平台 (UWP) 应用添加基于云的后端服务。 有关详细信息，请参阅 [什么是移动应用](app-service-mobile-value-prop.md)。 以下是完整应用的截屏：

![已完成的桌面应用](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

只有在完成本教程后，才可以学习有关 UWP 应用的所有其他移动应用教程。

## <a name="prerequisites"></a>必备组件

若要完成本教程，需要以下各项：

* 有效的 Azure 帐户。 如果没有帐户，可以注册 Azure 试用版并获取多达 10 个免费的移动应用，即使在试用期结束之后仍可继续使用这些应用。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。
* Windows 10。
* Visual Studio 社区2017。
* 熟悉 UWP 应用开发。 访问 [UWP 文档](https://docs.microsoft.com/windows/uwp/)，了解如何[进行设置](https://docs.microsoft.com/windows/uwp/get-started/get-set-up)，以便生成 UWP 应用。

## <a name="create-a-new-azure-mobile-app-backend"></a>创建新的 Azure 移动应用后端

按照下列步骤创建新的移动应用后端。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>创建数据库连接并配置客户端和服务器项目
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>运行客户端项目

1. 打开 UWP 项目。

2. 转到[Azure 门户](https://portal.azure.com/)，导航到所创建的移动应用。 在 `Overview` 边栏选项卡上，查找作为移动应用程序的公共终结点的 URL。 示例-我的应用名称 "test123" 的 sitename 将 https://test123.azurewebsites.net 。

3. 在此文件夹中打开 `App.xaml.cs` 的文件-"windows-uwp-cs/ZUMOAPPNAME/"。 应用程序名称为 `ZUMOAPPNAME`。

4. 在 `App` 类中，将 `ZUMOAPPURL` 参数替换为上面的公共终结点。

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    会
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. 按 F5 键部署并运行应用。

6. 在应用的“插入待办事项”文本框中键入有意义的文本（例如“完成教程”），并单击“保存”。

    ![Windows 快速入门完整桌面](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    这样可向在 Azure 中托管的新移动应用后端发送 POST 请求。

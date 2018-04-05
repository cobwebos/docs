---
title: 适用于 Unity Android 部署的 Azure Mobile Engagement 入门
description: 了解如何使用适用于 Unity 应用的具有分析和推送通知功能的 Azure Mobile Engagement 部署 iOS 设备。
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d5f0ef79-be00-4cec-97a5-a0b2fdaa380e
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: cd126fc656fc00667c4c09298409f920ab8160fc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-android-deployment"></a>适用于 Unity Android 部署的 Azure Mobile Engagement 入门
> [!IMPORTANT]
> Azure Mobile Engagement 已在 2018 年 3 月 31 日停用。 此页将在不久之后删除。
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主题介绍如何借助 Azure Mobile Engagement 了解应用的使用，以及如何在 Unity 应用程序部署到 Android 设备时，向其细分用户发送推送通知。
本教程以经典的 Unity 滚球教程为起点。 在继续进行我们会在下面教程中展示的 Mobile Engagement 集成之前，应按照本 [教程](mobile-engagement-unity-roll-a-ball.md) 中的步骤进行操作。 

本教程需要的内容如下：

* [Unity 编辑器](http://unity3d.com/get-unity)
* [Mobile Engagement Unity SDK](https://aka.ms/azmeunitysdk)
* Google Android SDK

> [!NOTE]
> 要完成本教程，必须有一个有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started)。
> 
> 

## <a id="setup-azme"></a>为 Android 应用设置 Mobile Engagement
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>将应用连接到 Mobile Engagement 后端
### <a name="import-the-unity-package"></a>导入 Unity 程序包
1. 下载 [Mobile Engagement Unity 程序包](https://aka.ms/azmeunitysdk)，将其保存到本地计算机。 
2. 转到“资产”->“导入包”->“自定义包”，并选择在上面步骤中下载的软件包。 
   
    ![][70] 
3. 请确保选择所有文件，并单击“导入”  按钮。 
   
    ![][71] 
4. 成功导入后，在项目中可以看到导入的 SDK 文件。  
   
    ![][72] 

### <a name="update-the-engagementconfiguration"></a>更新 EngagementConfiguration
1. 从 SDK 文件夹打开 **EngagementConfiguration** 脚本文件，并使用先前从 Azure 门户获取的连接字符串更新 **ANDROID\_CONNECTION\_STRING**。  
   
    ![][73]
2. 保存 
3. 执行“文件”->“参与”->“生成 Android 清单”。 这是由 Mobile Engagement SDK 添加的插件，单击此插件会自动更新项目设置。 
   
    ![][74]

> [!IMPORTANT]
> 请确保每次更新 **EngagementConfiguration** 文件时均执行此操作，否则所做的更改将不会反映在应用中。 
> 
> 

### <a name="configure-the-app-for-basic-tracking"></a>配置应用，以便进行基本的跟踪
1. 打开附加到 Player 对象的 **PlayerController** 脚本以供编辑。 
2. 添加以下 using 语句：
   
        using Microsoft.Azure.Engagement.Unity;
3. 将以下内容添加到 `Start()` 方法
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>部署和运行应用
请确保在尝试将此 Unity 应用部署到设备之前，计算机上已安装 Android SDK。 

1. 将 Android 设备连接到计算机。 
2. 打开“文件”->“生成设置” 
   
    ![][40]
3. 选择“Android”，并单击“切换平台”
   
    ![][51]
   
    ![][52]
4. 单击“播放机设置”  ，并提供有效的捆绑包标识符。 
   
    ![][53]
5. 最后，单击“ **生成并运行**
   
    ![][54]
6. 可能需要为文件夹命名，以便存储 Android 程序包。 
7. 如果一切正常，然后程序包将部署到连接的设备，并应在电话上看到 Unity 游戏！ 

## <a id="monitor"></a>将应用与实时监视相连接
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>启用推送通知和应用内消息传送
[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

### <a name="update-the-engagementconfiguration"></a>更新 EngagementConfiguration
1. 从 SDK 文件夹打开 **EngagementConfiguration** 脚本文件，并使用先前从 Google Cloud 开发人员门户获取的“Google 项目编号”更新 **ANDROID\_GOOGLE\_NUMBER**。 这是一个字符串值，请务必将其括在双引号内。 
   
    ![][75]
2. 保存文件。 
3. 执行“文件”->“参与”->“生成 Android 清单”。 这是由 Mobile Engagement SDK 添加的插件，单击此插件会自动更新项目设置。 
   
    ![][74]

### <a name="configure-the-app-to-receive-notifications"></a>配置应用以接收通知
1. 打开附加到 Player 对象的 **PlayerController** 脚本，进行编辑。 
2. 将以下内容添加到 `Start()` 方法
   
        EngagementReachAgent.Initialize();
3. 现在此应用已更新，请根据下面提供的说明在设备上部署并运行该应用。 

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[40]: ./media/mobile-engagement-unity-android-get-started/40.png
[70]: ./media/mobile-engagement-unity-android-get-started/70.png
[71]: ./media/mobile-engagement-unity-android-get-started/71.png
[72]: ./media/mobile-engagement-unity-android-get-started/72.png
[73]: ./media/mobile-engagement-unity-android-get-started/73.png
[74]: ./media/mobile-engagement-unity-android-get-started/74.png
[75]: ./media/mobile-engagement-unity-android-get-started/75.png
[51]: ./media/mobile-engagement-unity-android-get-started/51.png
[52]: ./media/mobile-engagement-unity-android-get-started/52.png
[53]: ./media/mobile-engagement-unity-android-get-started/53.png
[54]: ./media/mobile-engagement-unity-android-get-started/54.png

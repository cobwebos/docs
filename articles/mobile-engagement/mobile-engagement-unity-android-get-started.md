---
title: "开始使用 Azure Mobile Engagement Unity Android 部署"
description: "了解如何使用分析和推送通知的 Azure Mobile Engagement 用于 Unity 应用将部署到 iOS 设备。"
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: 
ms.assetid: d5f0ef79-be00-4cec-97a5-a0b2fdaa380e
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: bf0b758159d475b4ed7eadb84227e4824e11ba86
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-android-deployment"></a>开始使用 Azure Mobile Engagement Unity Android 部署
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主题说明如何使用 Azure Mobile Engagement 了解应用程序的使用情况和部署到 Android 设备时，如何发送通知推送到分段 Unity 应用程序的用户。
本教程使用经典 Unity 回滚球教程作为起始点。 应遵循在此步骤[教程](mobile-engagement-unity-roll-a-ball.md)再继续我们展示教程中的以下的 Mobile Engagement 集成。 

本教程的要求如下：

* [Unity 编辑器](http://unity3d.com/get-unity)
* [移动用户参与策略 Unity SDK](https://aka.ms/azmeunitysdk)
* Google Android SDK

> [!NOTE]
> 若要完成本教程，你必须具有有效的 Azure 帐户。 如果你没有帐户，可以在几分钟内创建一个免费试用帐户。 有关详细信息，请参阅[Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started)。
> 
> 

## <a id="setup-azme"></a>设置为你的 Android 应用的移动用户参与策略
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>将应用程序连接到 Mobile Engagement 后端
### <a name="import-the-unity-package"></a>导入 Unity 包
1. 下载[移动用户参与策略 Unity 包](https://aka.ms/azmeunitysdk)并将其保存到本地计算机。 
2. 转到**资产-> 导入包-> 自定义包**和在上述步骤中选择你下载的包。 
   
    ![][70] 
3. 请确保所有文件未选中，单击**导入**按钮。 
   
    ![][71] 
4. 成功导入后，你将看到你项目中的导入的 SDK 文件。  
   
    ![][72] 

### <a name="update-the-engagementconfiguration"></a>更新 EngagementConfiguration
1. 打开**EngagementConfiguration**从 SDK 文件夹和更新的脚本文件**ANDROID\_连接\_字符串**之前从 Azure 门户获取的连接字符串。  
   
    ![][73]
2. 保存文件 
3. 执行**文件-> 参与-> 生成 Android 清单**。 这是由 Mobile Engagement SDK 添加插件并单击它将自动更新你的项目设置。 
   
    ![][74]

> [!IMPORTANT]
> 请确保来执行此操作每次更新**EngagementConfiguration**文件否则为所做的更改将不会反映在应用程序。 
> 
> 

### <a name="configure-the-app-for-basic-tracking"></a>配置适用于基本跟踪的应用
1. 打开**PlayerController**脚本附加到播放器对象以进行编辑。 
2. 添加以下 using 语句：
   
        using Microsoft.Azure.Engagement.Unity;
3. 添加到以下`Start()`方法
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>部署并运行应用
请确保你有您的计算机上安装才尝试将此 Unity 应用部署到你的设备的 Android SDK。 

1. 连接到您的计算机的 Android 设备。 
2. 打开**文件-> 生成设置** 
   
    ![][40]
3. 选择**Android** ，然后单击**交换机平台**
   
    ![][51]
   
    ![][52]
4. 单击**播放器设置**并提供有效的捆绑标识符。 
   
    ![][53]
5. 最后单击**生成和运行**
   
    ![][54]
6. 你可能需要提供用于存储 Android 包文件夹名。 
7. 如果一切正常，然后将包部署到连接的设备，并应在手机上看到你的 Unity 游戏 ！ 

## <a id="monitor"></a>将应用程序连接了实时监视
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>启用推送通知和应用内消息传递
[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

### <a name="update-the-engagementconfiguration"></a>更新 EngagementConfiguration
1. 打开**EngagementConfiguration**从 SDK 文件夹和更新的脚本文件**ANDROID\_GOOGLE\_数**与**Google 项目编号**您之前获取从 Google 云开发人员门户。 这是一个字符串值因此请务必将它括在双引号内。 
   
    ![][75]
2. 保存该文件。 
3. 执行**文件-> 参与-> 生成 Android 清单**。 这是由 Mobile Engagement SDK 添加插件并单击它将自动更新你的项目设置。 
   
    ![][74]

### <a name="configure-the-app-to-receive-notifications"></a>配置应用以接收通知
1. 打开**PlayerController**脚本附加到播放器对象以进行编辑。 
2. 添加到以下`Start()`方法
   
        EngagementReachAgent.Initialize();
3. 现在，更新应用程序时，部署和每下面提供的说明进行操作的设备上运行应用程序。 

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

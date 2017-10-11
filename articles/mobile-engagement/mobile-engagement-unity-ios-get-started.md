---
title: "适用于 Unity iOS 部署的 Azure Mobile Engagement 入门"
description: "了解如何使用带有分析和推送功能的 Azure Mobile Engagement 将 Unity 应用部署到 iOS 设备。"
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7ddfbac3-8d13-4ebe-b061-c865f357297f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: c8f50404771965ec636065346ac04e059d264c3d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>适用于 Unity iOS 部署的 Azure Mobile Engagement 入门
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主题介绍如何借助 Azure Mobile Engagement 了解应用使用情况，以及如何在 Unity 应用程序部署到 iOS 设备时，向其用户段发送推送通知。
本教程以经典的 Unity 滚球教程为起点。 在继续进行我们会在下面教程中展示的 Mobile Engagement 集成之前，应按照本 [教程](mobile-engagement-unity-roll-a-ball.md) 中的步骤进行操作。 

本教程需要的内容如下：

* [Unity 编辑器](http://unity3d.com/get-unity)
* [Mobile Engagement Unity SDK](https://aka.ms/azmeunitysdk)
* XCode 编辑器

> [!NOTE]
> 要完成本教程，必须有一个有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started)。
> 
> 

## <a id="setup-azme"></a>为 iOS 应用设置 Mobile Engagement
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
1. 从 SDK 文件夹打开 **EngagementConfiguration** 脚本文件，并使用先前从 Azure 门户获取的连接字符串更新 **IOS\_CONNECTION\_STRING**。  
   
    ![][73]
2. 保存文件。 

### <a name="configure-the-app-for-basic-tracking"></a>配置应用，以便进行基本跟踪
1. 打开附加到 Player 对象的 **PlayerController** 脚本以供编辑。 
2. 添加以下 using 语句：
   
        using Microsoft.Azure.Engagement.Unity;
3. 将以下内容添加到 `Start()` 方法
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>部署和运行应用
1. 将 iOS 设备连接到计算机。 
2. 打开“文件”->“生成设置” 
   
    ![][40]
3. 选择“iOS”，并单击“切换平台”
   
    ![][41]
   
    ![][42]
4. 单击“播放机设置”  ，并提供有效的捆绑包标识符。 
   
    ![][53]
5. 最后，单击“生成并运行” 
   
    ![][54]
6. 可能需要为文件夹命名，才能存储 iOS 程序包。 
   
    ![][43]
7. 如果一切正常，将对该项目进行编译，而且应在 XCode 应用程序中将其打开。 
8. 请确保项目中的 **捆绑包标识符** 正确无误。  
   
    ![][75]
9. 立即在 XCode 中运行应用，以便将程序包部署到已连接的设备上，并在手机上查看 Unity 游戏！ 

## <a id="monitor"></a>将应用与实时监视相连接
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>启用推送通知和应用内消息传送
在市场推广活动中，可以使用 Mobile Engagement 通过推送通知和应用内消息传送与 REACH 模块和用户进行交互。 在 Mobile Engagement 门户中，此模块称为市场宣传。
无需在应用中进行任何额外配置即可接收通知，因为此功能已设置好。

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png

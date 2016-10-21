<properties
	pageTitle="适用于 Unity iOS 部署的 Azure Mobile Engagement 入门"
	description="了解如何使用带有分析和推送功能的 Azure Mobile Engagement 将 Unity 应用部署到 iOS 设备。"
	services="mobile-engagement"
	documentationCenter="unity"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-unity-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/19/2016"
	ms.author="piyushjo" />

# 适用于 Unity iOS 部署的 Azure Mobile Engagement 入门

[AZURE.INCLUDE [Hero 教程切换器](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主题将介绍如何借助 Azure Mobile Engagement 了解应用使用情况，以及如何在 Unity 应用程序部署到 iOS 设备时，向其用户段发送推送通知。本教程将以经典的 Unity 滚球教程为起点。在继续进行我们将在下面教程中展示的 Mobile Engagement 集成之前，应按照本[教程](mobile-engagement-unity-roll-a-ball.md)中的步骤进行操作。

本教程需要的内容如下：

+ [Unity 编辑器](http://unity3d.com/get-unity)
+ [Mobile Engagement Unity SDK](https://aka.ms/azmeunitysdk)
+ XCode 编辑器

> [AZURE.NOTE] 若要完成本教程，你必须有一个有效的 Azure 帐户。如果你没有帐户，只需花费几分钟就能创建一个免费试用帐户。有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-CN%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started)。

##<a id="setup-azme"></a>为 iOS 应用设置 Mobile Engagement

[AZURE.INCLUDE [在门户中创建 Mobile Engagement 应用](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>将应用连接到 Mobile Engagement 后端

###导入 Unity 程序包

1. 下载 [Mobile Engagement Unity 程序包](https://aka.ms/azmeunitysdk)，将其保存到本地计算机。

2. 转到“资产”->“导入程序包”->“自定义程序包”，然后选择在上一步骤中下载的程序包。

	![][70]

3. 请确保选择所有文件，然后单击“导入”按钮。

	![][71]

4. 成功导入后，在项目中可以看到导入的 SDK 文件。

	![][72]

###更新 EngagementConfiguration

1. 从 SDK 文件夹打开 **EngagementConfiguration** 脚本文件，并使用先前从 Azure 门户获取的连接字符串更新 **IOS\_CONNECTION\_STRING**。

	![][73]

2. 保存文件。

###配置应用，以便进行基本跟踪

1. 打开附加到 Player 对象的 **PlayerController** 脚本以供编辑。

2. 添加以下 using 语句：

		using Microsoft.Azure.Engagement.Unity;

3. 将以下内容添加到 `Start()` 方法
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###部署和运行应用

1. 将 iOS 设备连接到计算机。

2. 打开“文件”->“生成设置”

	![][40]

3. 选择“iOS”，然后单击“切换平台”

	![][41]

	![][42]

4. 单击“播放机设置”，并提供有效的捆绑包标识符。

	![][53]

5. 最后，单击“生成并运行”

	![][54]

6. 你可能需要为文件夹命名，才能存储 iOS 程序包。

	![][43]

7. 如果一切正常，将对该项目进行编译，而且应在 XCode 应用程序中将其打开。

8. 请确保项目中的**捆绑包标识符**正确无误。

	![][75]

10. 立即在 XCode 中运行应用，以便将程序包部署到已连接的设备上，并在手机上查看 Unity 游戏！

##<a id="monitor"></a>将应用与实时监视相连接

[AZURE.INCLUDE [将应用与实时监视相连接](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>启用推送通知和应用内消息传送

Mobile Engagement 使你可以在市场活动中，通过推送通知和应用内消息传送与用户交互和进行市场宣传。在 Mobile Engagement 门户中，此模块称为市场宣传。无需在应用中进行任何额外配置即可接收通知，因为此功能已设置好。

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

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

<!---HONumber=AcomDC_0921_2016-->
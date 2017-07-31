---
title: "Azure Mobile Engagement iOS SDK 集成 | Microsoft Docs"
description: "Azure Mobile Engagement 的 iOS SDK 的最新更新和过程"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 947ea44b-00c1-450f-9a3b-74437954dc56
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 01fdbb43c21ac6932e8462f4a6507fc63e50542d
ms.contentlocale: zh-cn
ms.lasthandoff: 07/20/2017

---
# <a name="how-to-integrate-engagement-on-ios"></a>如何在 iOS 上集成 Engagement
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
>
>

本过程介绍了在 iOS 应用程序中激活 Engagement 的分析和监视功能的最简单方法。

Engagement SDK 需要 iOS7+ 和 Xcode 8+：应用程序的部署目标必须至少为 iOS 7。

> [!NOTE]
> 如果确实需要使用 XCode 7，则可以使用 [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh)。 此旧版本的市场宣传模块在 iOS 10 设备上运行时存在一个已知问题。请参阅[市场宣传模块集成](mobile-engagement-ios-integrate-engagement-reach.md)了解更多详细信息。 如果选择使用 SDK v3.2.4，则直接跳过下一步中的 `UserNotifications.framework` 导入。
>
>

遵循以下步骤，即可激活用于计算所有用户、会话、活动、崩溃和技术相关统计数据所需的日志报告。 计算事件、错误和作业等其他统计信息所需的日志报告必须使用 Engagement API 手动完成（请参阅[如何在 iOS 应用中使用高级 Mobile Engagement 标记 API](mobile-engagement-ios-use-engagement-api.md)），因为这些统计信息均独立于应用程序。

## <a name="embed-the-engagement-sdk-into-your-ios-project"></a>在 iOS 项目中嵌入 Engagement SDK
* 在[此处](http://aka.ms/qk2rnj)下载 iOS SDK。
* 将 Engagement SDK 添加到 iOS 项目：在 Xcode 中，右键单击项目，选择“**将文件添加至...**”，然后选择 `EngagementSDK` 文件夹。
* Engagement 需要其他框架才能工作：在项目资源管理器中，打开项目窗格，并选择正确的目标。 然后，打开“**构建阶段**”选项卡，然后在“**将二进制文件链接到库**”菜单中添加框架：

  * `UserNotifications.framework` - 将链接设置为`Optional`
  * `AdSupport.framework` - 将链接设置为`Optional`
  * `SystemConfiguration.framework`
  * `CoreTelephony.framework`
  * `CFNetwork.framework`
  * `CoreLocation.framework`
  * `libxml2.dylib`

> [!NOTE]
> 可以删除 AdSupport 框架。 Engagement 需要此框架来收集 IDFA。 但是，可以禁用 IDFA 收集 \<ios-sdk-engagement-idfa\> 以符合有关此 ID 的新 Apple 策略。
>
>

## <a name="initialize-the-engagement-sdk"></a>初始化 Engagement SDK
需要修改应用程序代理：

* 在实现文件的顶部，导入 Engagement 代理：

      [...]
      #import "EngagementAgent.h"
* 在方法“**applicationDidFinishLaunching:**”或“**application:didFinishLaunchingWithOptions:**”中初始化 Engagement：

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
      {
        [...]
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
        [...]
      }

## <a name="basic-reporting"></a>基本报告
### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>建议方法：重载 `UIViewController` 类
若要激活 Engagement 计算用户、会话、活动、崩溃和技术统计信息所需的所有日志报告，只需从 `EngagementViewController`类继承所有 `UIViewController` 子类（与 `UITableViewController` -\> `EngagementTableViewController` 的规则相同）。

**没有 Engagement：**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**有 Engagement：**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>备用方法：手动调用 `startActivity()`
如果无法或不想重载 `UIViewController` 类，则可以通过直接调用 `EngagementAgent` 方法来启动活动。

> [!IMPORTANT]
> iOS SDK 将在应用程序关闭时自动调用 `endActivity()` 方法。 因此，每当用户的活动更改时，*强烈*建议调用 `startActivity` 方法，*从不*调用 `endActivity` 方法，因为调用此方法会强制结束当前的会话。
>
>

## <a name="location-reporting"></a>位置报告
Apple 服务条款不允许应用程序仅出于统计信息目的而使用位置跟踪。 因此，建议只在应用程序还出于其他原因需要使用位置跟踪时启用位置报告。

使用 iOS 8 启动时，必须通过在应用信息的 Info.plist 文件中设置键值 [NSLocationWhenInUseUsageDescription] 或 [NSLocationAlwaysUsageDescription] 的字符串来提供应用如何使用位置服务的说明。 如果要使用 Engagement 在后台中报告位置时，添加键值 NSLocationAlwaysUsageDescription。 在其他所有情况下，添加键值 NSLocationWhenInUseUsageDescription。 请注意，需要 NSLocationAlwaysAndWhenInUseUsageDescription 和 NSLocationWhenInUseUsageDescription 来报告 iOS 11 上的后台位置。

### <a name="lazy-area-location-reporting"></a>延迟区域位置报告
简易区域位置报告允许报告国家、地区和关联到设备的位置。 这种类型的位置报告仅使用网络位置（基于 Cell ID 或 WIFI）。 每个会话最多报告一次设备区域。 从不使用 GPS，因此，此类型的位置报告对电池的影响非常小（但不是没有）。

报告的区域用于计算用户、会话、事件和错误相关的地理统计信息， 还可用作市场宣传活动中的条件。 借助[设备 API] 可以检索为设备报告的最新已知区域。

若要启用简易区域位置报告，请在初始化 Engagement 代理后添加以下行：

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>实时位置报告
实时位置报告允许报告关联到设备的纬度和经度。 默认情况下，此类型的位置报告仅使用网络位置（基于 Cell ID 或 WIFI），且报告仅在应用程序在前台中运行时（例如，在会话期间）可用。

实时位置*不会*用于计算统计信息。 其唯一用途是，允许在市场宣传活动中使用实时地理范围 \<Reach-Audience-geofencing\> 标准。

若要启用实时位置报告，请在初始化 Engagement 代理后添加以下行：

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>基于 GPS 的报告
默认情况下，实时位置报告仅使用基于网络的位置。 若要启用基于 GPS 的位置（其更为精确），请添加：

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>后台报告
默认情况下，实时位置报告仅在应用程序在前台中运行时（例如，在会话期间）可用。 若要也在后台中启用报告，请添加：

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [!NOTE]
> 应用程序在后台中运行时，即使已启用 GPS，也仅报告基于网络的位置。
>
>

应用程序进入后台后，此功能的实现将调用 [startMonitoringSignificantLocationChanges]。 注意如果有新的位置事件到达，则此功能将自动在后台中重新启动应用程序。

## <a name="advanced-reporting"></a>高级报告
此外，如果你想报告应用程序特定事件、错误和作业，则需要通过 `EngagementAgent` 类的方法使用 Engagement API。 可以通过调用 `[EngagementAgent shared]` 静态方法检索该类的对象。

Engagement API 允许使用所有的 Engagement 高级功能，其详细信息位于“如何在 iOS 上使用 Engagement API”（及 `EngagementAgent` 类的技术文档中）。

## <a name="disable-idfa-collection"></a>禁用 IDFA 收集
默认情况下，Engagement 将使用 [IDFA] 来唯一标识用户。 但是如果未在应用中的其他位置使用广告宣传，则可能会被应用商店审查流程拒绝。 可以通过在 pch 文件（或在应用程序的 `Build Settings` 中）添加预处理器宏 `ENGAGEMENT_DISABLE_IDFA` 来禁用 IDFA 收集。 这可以确保在应用程序内部版本中不会存在对 `ASIdentifierManager``advertisingIdentifier` 或 `isAdvertisingTrackingEnabled` 的引用。

**prefix.pch** 文件中的集成：

    #define ENGAGEMENT_DISABLE_IDFA
    ...

可以通过检查 Engagement 测试日志来验证是否已在应用程序中正确禁用 IDFA 收集。 请参阅“集成测试”\<ios-sdk-engagement-test-idfa\> 文档了解更多信息。

## <a name="disable-log-reporting"></a>禁用日志报告
### <a name="using-a-method-call"></a>使用方法调用
如果想要 Engagement 停止发送日志，可调用：

    [[EngagementAgent shared] setEnabled:NO];

该调用具有持久性：它使用 `NSUserDefaults` 存储信息。

可通过调用 `YES` 的同一函数，即可再次启用日志报告。

### <a name="integration-in-your-settings-bundle"></a>设置捆绑包中的集成
还可以直接在现有的 `Settings.bundle` 文件中集成此设置，而不用再调用此函数。 该字符串 `engagement_agent_enabled` 必须用作首选项标识符，且必须关联到切换开关(`PSToggleSwitchSpecifier`)。

`Settings.bundle` 的以下示例显示了如何实现它：

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[设备 API]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier


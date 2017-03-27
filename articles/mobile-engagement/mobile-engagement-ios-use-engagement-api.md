---
title: "如何在 iOS 上使用 Engagement API"
description: "最新 iOS SDK - 如何在 iOS 上使用 Engagement API"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1fb4509e-3804-46c1-949f-1cf727f91f9f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bf672384407588ddc2c4998f42f6893e2638c592
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-use-the-engagement-api-on-ios"></a>如何在 iOS 上使用 Engagement API
本文档是“如何在 iOS 上集成 Engagement”文档的附加内容：它提供了有关如何使用 Engagement API 报告应用程序统计信息的全部详细信息。

请记住，如果只希望 Engagement 报告应用程序的会话、活动、故障和技术信息，则最简单的方法是从相应 `EngagementViewController` 类继承所有自定义的 `UIViewController` 类。

如需更多报告，例如，如需报告应用程序特定事件、错误和作业，或必须使用不同于在 `EngagementViewController` 类中实施的方式报告应用程序的活动，则需使用 Engagement API。

Engagement API 由 `EngagementAgent` 类提供。 此类的实例可通过调用 `[EngagementAgent shared]` 静态方法检索（请注意，返回的 `EngagementAgent` 对象是单一实例）。

进行任何 API 调用前，必须通过调用方法 `[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];` 初始化 `EngagementAgent` 对象

## <a name="engagement-concepts"></a>Engagement 概念
以下部分针对 iOS 平台优化常见的 [Mobile Engagement 概念](mobile-engagement-concepts.md)。

### <a name="session-and-activity"></a>`Session` 和 `Activity`
*活动*通常与应用程序的一个屏幕相关联，即，*活动*在屏幕显示时开始，并在屏幕关闭时停止：当使用 `EngagementViewController` 类对 Engagement SDK 进行集成时，会出现此情况。

但也可通过使用 Engagement API 手动控制*活动*。 这样就可以在若干子部分中拆分给定的屏幕，以获取有关该屏幕使用情况的详细信息（例如，了解对话框在该屏幕中使用的频率和时长）。

## <a name="reporting-activities"></a>报告活动
### <a name="user-starts-a-new-activity"></a>用户启动新活动
            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

需在每次用户活动更改时调用 `startActivity()`。 对此函数的第一次调用会启动一个新的用户会话。

### <a name="user-ends-his-current-activity"></a>用户结束其当前活动
            [[EngagementAgent shared] endActivity];

> [!WARNING]
> 应**从不**自行调用此函数，想要将应用程序的一种用途拆分到多个会话时除外：调用此函数会立即结束当前会话，因此，对 `startActivity()` 的后续调用会启动新的会话。 应用程序处于关闭状态时，SDK 自动调用此函数。
> 
> 

## <a name="reporting-events"></a>报告事件
### <a name="session-events"></a>会话事件
会话事件通常用于报告用户在会话期间执行的操作。

**无其他数据的示例：**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**具有其他数据的示例：**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>独立事件
与会话事件相反，独立事件可用于会话的上下文的外部。

**示例：**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

## <a name="reporting-errors"></a>报告错误
### <a name="session-errors"></a>会话错误
会话错误通常用于报告在用户会话期间影响用户的错误。

**示例：**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>独立错误
与会话错误相反，独立错误可用于会话的上下文的外部。

**示例：**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

## <a name="reporting-jobs"></a>报告作业
**示例：**

假设想要报告登录过程的持续时间：

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>在作业期间报告错误
错误可与运行作业相关，而不是与当前用户会话相关。

**示例：**

假设想要报告登录过程中出现的错误：

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>作业期间的事件
事件可与运行作业相关，而不是与当前用户会话相关。

**示例：**

假设我们有一个社交网络，并使用作业来报告用户被连接到服务器所需的总时间。 用户可接收来自朋友的消息，这是作业事件。

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

## <a name="extra-parameters"></a>其他参数
任意数据可以附加到事件、错误、活动和作业。

可以将该数据结构化，此数据使用 iOS 的 NSDictionary 类。

请注意，额外信息可以包含 `arrays(NSArray, NSMutableArray)`、`numbers(NSNumber class)`、`strings(NSString, NSMutableString)`、`urls(NSURL)`, `data(NSData, NSMutableData)` 或其他 `NSDictionary` 实例。

> [!NOTE]
> 其他参数使用 JSON 进行序列化。 如果想要传递不同于上述的对象，则必须在类中实现以下方法：
> 
> -(NSString*)JSONRepresentation;
> 
> 该方法应返回对象的 JSON 表示形式。
> 
> 

### <a name="example"></a>示例
    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>限制
#### <a name="keys"></a>密钥
`NSDictionary` 中的每个密钥必须与以下正则表达式匹配：

`^[a-zA-Z][a-zA-Z_0-9]*`

这意味着，密钥必须以至少一个字母开头，后跟字母、数字或下划线（\_）。

#### <a name="size"></a>大小
其他参数的限制为每次调用 **1024** 个字符（通过 Engagement 代理在 JSON 中编码后）。

在上一示例中，发送到服务器的 JSON 长度为 58 个字符：

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>报告应用程序信息
可使用 `sendAppInfo:` 函数手动报告跟踪信息（或任何其他应用程序特定信息）。

请注意，这些信息可以增量方式发送：仅为给定设备保留给定密钥的最新值。

与事件 Extras 类似，`NSDictionary` 类被用于应用程序信息的摘要，请注意，数组或子字典将被视为平面字符串（使用 JSON 序列化）。

**示例：**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>限制
#### <a name="keys"></a>密钥
`NSDictionary` 中的每个密钥必须与以下正则表达式匹配：

`^[a-zA-Z][a-zA-Z_0-9]*`

这意味着，密钥必须以至少一个字母开头，后跟字母、数字或下划线（\_）。

#### <a name="size"></a>大小
应用程序信息仅限于每次调用 **1024** 个字符（通过 Engagement 代理以 JSON 编码后）。

在上一示例中，发送到服务器的 JSON 长度为 44 个字符：

    {"birthdate":"1983-12-07","gender":"female"}


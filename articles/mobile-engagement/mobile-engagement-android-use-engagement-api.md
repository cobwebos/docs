---
title: "如何在 Android 上使用 Engagement API"
description: "最新 Android SDK - 如何在 Android 上使用 Engagement API"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 09b62659-82ae-4a55-8784-fca0b6b22eaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: d353cd2fe47c54a0282cc5bb1b22b4a56e0cd82c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-the-engagement-api-on-android"></a>如何在 Android 上使用 Engagement API
此文档是文档  [Android Mobile Engagement SDK 的高级报告选项](mobile-engagement-android-advanced-reporting.md)的外接程序。 它提供了有关如何使用 Engagement API 来报告应用程序统计信息的全部详细信息。

请记住，如果只希望 Engagement 报告应用程序的会话、活动、故障和技术信息，则最简单的方法是从相应 `Activity` 类继承所有 `EngagementActivity` 子类。

如需更多报告，例如，如需报告应用程序特定事件、错误和作业，或必须使用不同于在 `EngagementActivity` 类中实施的方式报告应用程序的活动，则需使用 Engagement API。

Engagement API 由 `EngagementAgent` 类提供。 此类的实例可通过调用 `EngagementAgent.getInstance(Context)` 静态方法检索（请注意，返回的 `EngagementAgent` 对象是单一实例）。

## <a name="engagement-concepts"></a>Engagement 概念
以下部分针对 Android 平台优化常见的 [Mobile Engagement 概念](mobile-engagement-concepts.md)。

### <a name="session-and-activity"></a>`Session` 和 `Activity`
如果用户在两个*活动*间的空闲时间超过几秒以上，则其*活动*序列会被拆分为两个不同的*会话*。 这几秒被称为“会话超时”。

*活动*通常与应用程序的一个屏幕相关联，即，*活动*在屏幕显示时开始，并在屏幕关闭时停止：当使用 `EngagementActivity` 类对 Engagement SDK 进行集成时，会出现此情况。

但也可通过使用 Engagement API 手动控制*活动*。 这样就可以在若干子部分中拆分给定的屏幕，以获取有关该屏幕使用情况的详细信息（例如，了解对话框在该屏幕中使用的频率和时长）。

## <a name="reporting-activities"></a>报告活动
> [!IMPORTANT]
> 如果使用 `EngagementActivity` 类及其变体（如“如何在 Android 上集成 Engagement”的文档中所述），则无需报告该部分中所述的活动。
> 
> 

### <a name="user-starts-a-new-activity"></a>用户启动新活动
            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

需在每次用户活动更改时调用 `startActivity()`。 对此函数的第一次调用会启动一个新的用户会话。

调用此函数的最佳位置位于每个活动 `onResume` 回调上。

### <a name="user-ends-his-current-activity"></a>用户结束其当前活动
            EngagementAgent.getInstance(this).endActivity();

需在用户结束其最后活动时至少调用一次 `endActivity()`。 该调用将通知 Engagement SDK：用户当前处于空闲状态，且用户会话需在会话超时到期时关闭（如果在会话超时到期前调用  `startActivity()`，则会恢复该会话）。

调用此函数的最佳位置位于每个活动 `onPause` 回调上。

## <a name="reporting-events"></a>报告事件
### <a name="session-events"></a>会话事件
会话事件通常用于报告用户在会话期间执行的操作。

**无其他数据的示例：**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**具有其他数据的示例：**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>独立事件
与会话事件相反，独立事件可在会话的上下文的外部出现。

**示例：**

假设想要报告触发广播接收器时出现的事件：

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

## <a name="reporting-errors"></a>报告错误
### <a name="session-errors"></a>会话错误
会话错误通常用于报告在用户会话期间影响用户的错误。

**示例：**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>独立错误
与会话错误相反，独立错误可在会话的上下文的外部出现。

**示例：**

以下示例显示了如何报告在应用程序进程运行时手机上的内存在任何时候降低时的错误。

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

## <a name="reporting-jobs"></a>报告作业
### <a name="example"></a>示例
假设想要报告登录过程的持续时间：

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>在作业期间报告错误
错误可与运行作业相关，而不是与当前用户会话相关。

**示例：**

假设想要报告登录过程中出现的错误：

[...] public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>在作业期间报告事件
事件可与运行作业相关，而不是与当前用户会话相关。

**示例：**

假设我们有一个社交网络，并使用作业来报告用户被连接到服务器所需的总时间。 用户在使用其他应用程序或其手机处于休眠时仍可后台保持连接状态。

用户可接收来自朋友的消息，这是作业事件。

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

## <a name="extra-parameters"></a>其他参数
任意数据可以附加到事件、错误、活动和作业。

可对该数据结构化，它使用 Android 的 Bundle 类（实际上，其工作原理类似 Android Intents 中的额外参数）。 请注意，一个 Bundle 可包含数组或其他 Bundle 实例。

> [!IMPORTANT]
> 如果加入 Parcelable 或 Serializable 参数，请确保实现其 `toString()` 方法以返回用户可读的字符串。 调用 `bundle.putSerializable("key",value);` 时，包含非暂时性字段的非序列化的 Serializable 类会使 Android 崩溃。
> 
> [!WARNING]
> 不支持额外参数中的 Sparse 数组，即，它不会被序列化为一个数组。 在额外参数中使用此类数组前，应将其转换为标准数组。
> 
> 

### <a name="example"></a>示例
            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>限制
#### <a name="keys"></a>密钥
`Bundle` 中的每个密钥必须与以下正则表达式匹配：

`^[a-zA-Z][a-zA-Z_0-9]*`

这意味着，密钥必须以至少一个字母开头，后跟字母、数字或下划线（\_）。

#### <a name="size"></a>大小
其他信息仅限于每次调用 **1024** 个字符（通过 Engagement 服务在 JSON 中编码后）。

在上一示例中，发送到服务器的 JSON 长度为 58 个字符：

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>报告应用程序信息
可使用 `sendAppInfo()` 函数手动报告跟踪信息（或任何其他应用程序特定信息）。

请注意，这些信息可以增量方式发送：仅为给定设备保留给定密钥的最新值。

与事件 Extras 类似，Bundle 类被用于应用程序信息的摘要，请注意，数组或 sub-bundles 会被视为平面字符串（使用 JSON 序列化）。

### <a name="example"></a>示例
以下是发送用户性别和出生日期的代码示例：

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>限制
#### <a name="keys"></a>密钥
`Bundle` 中的每个密钥必须与以下正则表达式匹配：

`^[a-zA-Z][a-zA-Z_0-9]*`

这意味着，密钥必须以至少一个字母开头，后跟字母、数字或下划线（\_）。

#### <a name="size"></a>大小
应用程序信息仅限于每次调用 **1024** 个字符（通过 Engagement 服务在 JSON 中编码后）。

在上一示例中，发送到服务器的 JSON 长度为 44 个字符：

            {"expiration":"2016-12-07","status":"premium"}

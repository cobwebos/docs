---
title: "Azure Mobile Engagement Android SDK 的高级配置"
description: "介绍高级配置选项，包括 Azure Mobile Engagement Android SDK 的 Android 清单。"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 37d2c09a-86fa-473d-8987-c7e35a0eb3e8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 0301f71c76872714aa1bf727a6c21dd7a63db036
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Azure Mobile Engagement Android SDK 的高级配置
> [!div class="op_single_selector"]
> * [通用 Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
>
>

此过程描述如何为 Azure Mobile Engagement Android 应用配置各种配置选项。

## <a name="prerequisites"></a>先决条件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>权限要求
在特定功能中，有些选项需要特定权限（在此一并列出以供参考）以及内联。 将这些权限添加到项目 AndroidManifest.xml 的 `<application>` 标记之前或之后。

权限代码需如下类似格式，可以根据下表填入相应权限。

    <uses-permission android:name="android.permission.[specific permission]"/>


| 权限 | 在使用时 |
| --- | --- |
| INTERNET |必需。 针对基本报告 |
| ACCESS_NETWORK_STATE |必需。 针对基本报告 |
| RECEIVE_BOOT_COMPLETED |必需。 在设备重启后显示通知中心 |
| WAKE_LOCK |推荐。 使用 WiFi 或在屏幕处于关闭状态时启用收集数据。 |
| 振动 |可选。 收到通知时启用震动 |
| DOWNLOAD_WITHOUT_NOTIFICATION |可选。 启用 Android 大图通知 |
| WRITE_EXTERNAL_STORAGE |可选。 启用 Android 大图通知 |
| ACCESS_COARSE_LOCATION |可选。 启用实时位置报告 |
| ACCESS_FINE_LOCATION |可选。 启用基于 GPS 的位置报告 |

从 Android M 开始，[某些权限在运行时管理](mobile-engagement-android-location-reporting.md#android-m-permissions)。

如已在使用 ``ACCESS_FINE_LOCATION``，则无需也使用 ``ACCESS_COARSE_LOCATION``。

## <a name="android-manifest-configuration-options"></a>Android 清单配置选项
### <a name="crash-report"></a>崩溃报告
若要禁用崩溃报告，在 `<application>` 和 `</application>` 标记间添加该代码：

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>突发阈值
默认情况下，Engagement 服务实时报告日志。 如果应用程序报告日志经常改变，则最好对日志进行缓冲并在固定的时间一次性进行报告（称为“突发模式”）。 若要执行此操作，请在 `<application>` 和 `</application>` 标记间添加此代码：

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

突发模式虽然会略微增加电池寿命，但将对 Engagement 监视器造成影响：所有会话和作业持续时间均被取整为突发阈值（因此，短于突发阈值的会话和作业可能不可见）。 突发阈值不应长于 30000（30 秒）。

### <a name="session-timeout"></a>会话超时
 可通过按 **Home** 键或 **Back** 键，设置手机空闲或跳转至其他应用程序来结束某个活动。 默认情况下，会话会在其最后一个活动结束 10 秒后结束。 这可避免用户每次快速退出和返回应用程序时的会话拆分（用户在执行选取图像、检查通知等操作时可能出现此问题）。可能需要修改这个参数。 若要执行此操作，请在 `<application>` 和 `</application>` 标记间添加此代码：

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>禁用日志报告
### <a name="using-a-method-call"></a>使用方法调用
如果想要 Engagement 停止发送日志，可调用：

    EngagementAgent.getInstance(context).setEnabled(false);

该调用是持续性的：它使用共享的首选项文件。

如果 Engagement 在调用此函数时处于活动状态，则可能需要一分钟来停止该服务。 但在下次启动应用程序时，该调用将完全不会启动该服务。

可通过调用 `true` 的同一函数，即可再次启用日志报告。

### <a name="integration-in-your-own-preferenceactivity"></a>在自己的 `PreferenceActivity` 中集成
如不调用此函数，还可以在现有的 `PreferenceActivity` 中直接集成此设置。

可在 `AndroidManifest.xml` 文件中使用 `application meta-data`，配置 Engagement，以使用首选项文件：

* `engagement:agent:settings:name` 关键字用于定义共享的首选项文件的名称。
* `engagement:agent:settings:mode` 关键字用于定义共享的首选项文件的模式。 使用与 `PreferenceActivity` 中相同的模式。 该模式必须以数字传递：如果在代码中使用常量标志的组合，请检查总值。

Engagement 在首选项文件内始终使用 `engagement:key` 布尔型关键字，以用于管理该设置。

以下的 `AndroidManifest.xml` 示例显示默认值：

    <application>
        [...]
        <meta-data
          android:name="engagement:agent:settings:name"
          android:value="engagement.agent" />
        <meta-data
          android:name="engagement:agent:settings:mode"
          android:value="0" />

然后，可在首选项布局中添加 `CheckBoxPreference`，如下所示：

    <CheckBoxPreference
      android:key="engagement:enabled"
      android:defaultValue="true"
      android:title="Use Engagement"
      android:summaryOn="Engagement is enabled."
      android:summaryOff="Engagement is disabled." />

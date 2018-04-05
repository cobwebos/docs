---
title: Azure Mobile Engagement Android SDK 集成
description: Azure Mobile Engagement Android SDK 的最新更新和过程
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 585341f9-3f39-459a-af42-864e400a0128
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: a01bd74649fa28e0d86cea4ed03da80326c06234
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="release-notes"></a>发行说明
> [!IMPORTANT]
> Azure Mobile Engagement 已在 2018 年 3 月 31 日停用。 此页将在不久之后删除。
> 


## <a name="431-07172017"></a>4.3.1 (07/17/2017)
* 修复在调用 `EngagementAgentUtils.isInDedicatedEngagementProcess`（`EngagementApplication` 类也使用）时可能很少发生的故障。

## <a name="430-06272017"></a>4.3.0 (06/27/2017)
* Android 8 支持（以前版本的 SDK 在 Android 8 上无法使用）。
* 不再存在对支持库的依赖性。
* 删除 `EngagementFragmentActivity` 类。
* 由于 Android 8 的[后台执行限制](https://developer.android.com/preview/features/background.html)，后台日志可能延迟到用户与设备交互时生成，这将在设备进入睡眠状态的情况下对延迟的“已发送”推送营销活动和“显示的系统通知”统计信息产生影响（仍将显示通知，并且将实时响铃和振动，在这些方面没有问题）。
* 由于[后台位置限制](https://developer.android.com/preview/features/background-location-limits.html)，Android 8 上的实时后台位置不会经常更新。

## <a name="424-03302017"></a>4.2.4 (03/30/2017)
* 将 Android 7 中的应用内通知文本颜色修复为与之前 Android 版本中相同。

## <a name="423-08102016"></a>4.2.3 (08/10/2016)
* 不再存在任何 WIFI 锁。
* 修复了初始化前调用 getDeviceId 时出现的死锁状态（4.2.0 中介绍的问题）。

## <a name="422-05172016"></a>4.2.2 (05/17/2016)
* 稳定性改进。

## <a name="421-05102016"></a>4.2.1 (05/10/2016)
* 安全性：禁用 Web 视图本地文件访问权限。
* 安全性：删除扩展已过时且不安全的 `PreferenceActivity` 类的 `EngagementPreferenceActivity` 类。
* 安全性：现在市场宣传活动已得到记录以使用 `exported="false"`，此标志也可以在以前的 SDK 版本中使用。

## <a name="420-03112016"></a>4.2.0 (03/11/2016)
* SDK 现在已通过 MIT 许可。
* 允许在 SDK 初始化时指定自定义设备标识符。

## <a name="415-02012016"></a>4.1.5 (02/01/2016)
* 稳定性改进。

## <a name="414-01262016"></a>4.1.4 (01/26/2016)
* 稳定性改进。

## <a name="413-1292015"></a>4.1.3 (12/9/2015)
* 稳定性改进。

## <a name="412-11252015"></a>4.1.2 (11/25/2015)
* 稳定性改进。

## <a name="411-11042015"></a>4.1.1 (11/04/2015)
* 稳定性改进。

## <a name="410-08252015"></a>4.1.0 (08/25/2015)
* 处理 Android M 的新权限模型。
* 现在，可以在运行时配置位置功能，而不是使用 `AndroidManifest.xml`。
* 修复了权限 bug：如果使用 `ACCESS_FINE_LOCATION`，则不再需要 `ACCESS_COARSE_LOCATION`。
* 稳定性改进。

## <a name="400-07062015"></a>4.0.0 (07/06/2015)
* 内部协议更改使分析和推送更加可靠。
* 原生推送 (GCM/ADM) 现在也用于应用内通知，因此，必须为任何类型的推送市场活动配置原生推送凭据。
* 修复大图通知：推送后仅需 10 秒即可显示。
* 修复 Web 视图中的 bug：单击链接还会执行默认操作 URL。
* 修复与本地存储管理相关的极少数崩溃。
* 修复动态配置字符串管理。
* 更新 EULA。

## <a name="300-02172015"></a>3.0.0 (02/17/2015)
* Azure Mobile Engagement 的初始版本
* appId 配置将替换为连接字符串配置。
* 删除了发送和接收来自任意 XMPP 实体的任意 XMPP 消息的 API。
* 删除了在设备之间发送和接收消息的 API。
* 安全性改进。
* 删除了 Google Play 和 SmartAd 跟踪。


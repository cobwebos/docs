---
title: "Azure Mobile Engagement Android SDK 集成"
description: "Azure Mobile Engagement Android SDK 的最新更新和过程"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 585341f9-3f39-459a-af42-864e400a0128
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: af776804affea5abe599594d7b486bbead982f3e
ms.lasthandoff: 11/17/2016


---
# <a name="release-notes"></a>发行说明
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
* 修复了权限 bug：如果你使用 `ACCESS_FINE_LOCATION`，则不再需要 `ACCESS_COARSE_LOCATION`。
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



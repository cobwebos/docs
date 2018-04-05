---
title: Azure Mobile Engagement iOS SDK 发行说明 | Microsoft Docs
description: Azure Mobile Engagement 的 iOS SDK 的最新更新和过程
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a43ff0f6-90d5-4b3c-8d7a-a1db21bc776b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 0268d65d2c0ea3cefeb8f06793838bc263e443bd
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Azure Mobile Engagement iOS SDK 发行说明
> [!IMPORTANT]
> Azure Mobile Engagement 已在 2018 年 3 月 31 日停用。 此页将在不久之后删除。
> 


## <a name="410-07172017"></a>4.1.0 (07/17/2017)
* 修复了后台清除的锁屏提醒。
* 修复了 XCode 9 上有关主队列中未调用的 API 的警告。
* 修复了 Reach 投票模块中的内存泄漏。
* 放弃了对 iOS 6.X 的支持。 从此版本开始，应用程序的部署目标必须至少为 iOS 7。

## <a name="401-12132016"></a>4.0.1 (12/13/2016)
* 改进了后台的日志传送。

## <a name="400-09122016"></a>4.0.0 (09/12/2016)
* 修复了 iOS 10 设备上不起作用的通知。
* 弃用了 XCode 7。

## <a name="324-06302016"></a>3.2.4 (06/30/2016)
* 修复了技术日志和其他日志之间的聚合。

## <a name="323-06072016"></a>3.2.3 (06/07/2016)
* 修复了应用在后台时不报告送达反馈的 bug。
* 优化了技术日志的发送。

## <a name="322-04072016"></a>3.2.2 (04/07/2016)
* 修复了 HTTP 请求取消时有时导致崩溃的 bug。

## <a name="321-12112015"></a>3.2.1 (12/11/2015)
* 修复了由含有深层链接的通知触发新应用实例时的延迟。

## <a name="320-10082015"></a>3.2.0 (10/08/2015)
* 在 SDK 中启用了 Bitcode，以使其使用 **Xcode 7**。
* 修复了与应用内通知相关的 bug。
* 使应用内通知在电池电量不足和其他此类情况下更可靠。
* 删除了由第三方库生成的额外控制台日志。

## <a name="310-08262015"></a>3.1.0 (08/26/2015)
* 修复了与第三方库的 iOS 9 兼容性 bug。 发送投票结果、应用程序信息或额外的数据时，它会导致崩溃。

## <a name="300-06192015"></a>3.0.0 (06/19/2015)
* Mobile Engagement 使用无提示推送通知。
* 放弃了对 iOS 4.X 的支持。 从此版本开始，应用程序的部署目标必须至少为 iOS 6。

## <a name="220-05212015"></a>2.2.0 (05/21/2015)
* 低于 iOS 6 的设备的 Mobile Engagement 设备 ID 现在基于安装时生成的 GUID。

## <a name="210-04242015"></a>2.1.0 (04/24/2015)
* 增加了 Swift 兼容性。
* 现在单击通知时，在打开应用程序之后立即执行操作 URL。
* 添加了 SDK 包中缺少的标头文件。
* 解决了禁用 Mobile Engagement 崩溃报告器时出现的问题。

## <a name="200-02172015"></a>2.0.0 (02/17/2015)
* Azure Mobile Engagement 的初始版本
* appId/sdkKey 配置将替换为连接字符串配置。
* 删除了发送和接收来自任意 XMPP 实体的任意 XMPP 消息的 API。
* 删除了在设备之间发送和接收消息的 API。
* 安全性改进。
* 删除了 SmartAd 跟踪。

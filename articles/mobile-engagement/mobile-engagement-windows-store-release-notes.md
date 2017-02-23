---
title: "Azure Mobile Engagement Windows 通用应用 SDK 发行说明 | Microsoft Docs"
description: "Azure Mobile Engagement - Windows 通用应用 SDK 发行说明"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: de938ce5-93d5-4218-9e33-10eef102bd61
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 51858d6daca27a75e7f87af507600c7a193c874f
ms.openlocfilehash: dc5529a9e8f4eba867732f719ca8fff718c00d5a


---
# <a name="windows-universal-apps-sdk-release-notes"></a>Windows 通用应用 SDK 发行说明
## <a name="341-11032016"></a>3.4.1 (11/03/2016)

* 稳定性改进。

## <a name="340-04192016"></a>3.4.0 (04/19/2016)
* 市场宣传覆盖改进。
* 添加了“TestLogLevel”API，以启用/禁用/筛选由 SDK 发出的控制台日志。
* 修复了定位到在应用启动时未显示的第一个活动的活动通知。

## <a name="331-02182016"></a>3.3.1 (02/18/2016)
* 修复了 Web 公告的 HTML 内容和 SDK 的 HTML 页面之间的冲突。
* 稳定性改进。

## <a name="330-01222016"></a>3.3.0 (01/22/2016)
* 修复了在格式化以发布模式执行的 UWP 应用时的崩溃问题。
* 修复了 Universal 8.1 应用的通知上的 1 像素边距的问题。
* 在操作 URL 上现在可以使用 ms-appx 和 ms-appdata 方案。
* 稳定性改进。

## <a name="320-11202015"></a>3.2.0 (11/20/2015)
* 添加了对 Windows 10 通用 Windows 平台应用程序的支持。
* 添加了推送通道共享功能，以修复通道冲突问题（现在与 Azure 通知中心兼容）。
* 修复了初始化后立即请求设备 ID 时崩溃的问题。
* 控制台日志改进。
* 修复了分析某些未经处理的异常时发生崩溃的问题。

## <a name="310-05212015"></a>3.1.0 (05/21/2015)
* Mobile Engagement 设备 ID 现在是基于安装时生成的 GUID。

## <a name="301-04292015"></a>3.0.1 (04/29/2015)
* 修复了在某些 Windows Phone WinRT 应用上影响 SDK 初始化的 bug。

## <a name="300-04032015"></a>3.0.0 (04/03/2015)
* 引入了针对通用应用（Windows 和 Windows Phone WinRT）的 Mobile Engagement SDK。
* 更新了默认通知图标。
* 单击通知时，会返回系统通知操作反馈。
* 修复了系统通知有时在单击后会在应用内重播的问题。

## <a name="200-02172015"></a>2.0.0 (02/17/2015)
* Azure Mobile Engagement 的初始版本
* appId/sdkKey 配置将替换为连接字符串配置。
* 安全性改进。




<!--HONumber=Feb17_HO2-->



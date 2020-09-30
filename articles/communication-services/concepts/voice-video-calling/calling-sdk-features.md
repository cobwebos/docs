---
title: Azure 通信服务呼叫客户端概述
titleSuffix: An Azure Communication Services concept document
description: 概述呼叫客户端库。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 6021bb4fa5d30de423ab56bfb5fdea6af2665a4a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334474"
---
# <a name="calling-client-library-overview"></a>呼叫客户端库概述

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

呼叫客户端库有两个不同的系列，分别面向客户端和服务 。 目前可用的客户端库适用于最终用户体验：网站和本机应用。

服务客户端库尚不可用，该库提供对原始语音和视频数据平面的访问权限，适用于与机器人和其他服务的集成。

## <a name="calling-client-library-capabilities"></a>呼叫客户端库功能

下表列出了 Azure 通信服务呼叫客户端库中当前可用的一组功能。

| 功能组 | 功能                                                                                                          | JS  | Java (Android) | Objective-C (iOS) 
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| 核心功能 | 在两个用户之间启动一对一呼叫                                                                           | ✔️   | ✔️            | ✔️  
|                   | 为两个以上的用户（最多 350 个用户）启动组呼叫                                                       | ✔️   | ✔️            | ✔️ 
|                   | 将两个用户的一对一呼叫升级为两个以上用户的组呼叫                                 | ✔️   | ✔️            | ✔️ 
|                   | 在一个组呼叫开始后加入它                                                                              | ✔️   | ✔️            | ✔️ 
|                   | 邀请其他 VoIP 参与者加入正在进行的组呼叫                                                       | ✔️   | ✔️            | ✔️ 
|                   | 将麦克风静音/取消静音                                                                                                     | ✔️   | ✔️            | ✔️         
|                   | 切换照相机                                                                                              | ✔️   | ✔️            | ✔️           
|                   | 本地保持/取消保持                                                                                                  | ✔️   | ✔️            | ✔️           
|                   | 活动说话人                                                                                                      | ✔️   | ✔️            | ✔️           
|                   | 选择通话扬声器                                                                                            | ✔️   | ✔️            | ✔️           
|                   | 选择呼叫麦克风                                                                                         | ✔️   | ✔️            | ✔️           
|                   | 显示参与者的状态<br/>空闲、早期媒体、正在连接、已连接、等候接听、接听中、已断开连接         | ✔️   | ✔️            | ✔️           
|                   | 显示呼叫的状态<br/>早期媒体、正在传入、正在连接、正在响铃、已连接、通话保持、正在断开连接、已断开连接 | ✔️   | ✔️            | ✔️           
|                   | 显示参与者是否静音                                                                                      | ✔️   | ✔️            | ✔️           
|                   | 显示参与者退出通话的原因                                                                       | ✔️   | ✔️            | ✔️     
| 屏幕共享    | 在应用程序中共享整个屏幕                                                                 | ✔️   | ❌            | ❌           
|                   | 共享特定的应用程序（从正在运行的应用程序列表中）                                                | ✔️   | ❌            | ❌           
|                   | 从打开的选项卡列表中共享 web 浏览器选项卡                                                                  | ✔️   | ❌            | ❌           
|                   | 参与者可以查看远程屏幕共享                                                                            | ✔️   | ✔️            | ✔️         
| 名单            | 列出参与者                                                                                                   | ✔️   | ✔️            | ✔️           
|                   | 删除参与者                                                                                                | ✔️   | ✔️            | ✔️         
| PSTN              | 启动与 PSTN 参与者的一对一呼叫                                                                     | ✔️   | ✔️            | ✔️   
|                   | 与 PSTN 参与者进行组呼叫                                                                           | ✔️   | ✔️            | ✔️
|                   | 将 PSTN 参与者的一对一呼叫升级为组呼叫                                                 | ✔️   | ✔️            | ✔️
|                   | 以 PSTN 参与者身份在组呼叫中拨出                                                                    | ✔️   | ✔️            | ✔️   
| 常规           | 使用音频测试服务测试麦克风、扬声器和照相机（可通过呼叫 8:echo123 使用）                   |  ✔️  | ✔️            | ✔️   

## <a name="calling-client-library-browser-support"></a>呼叫客户端库浏览器支持

下表显示了当前可用的一组受支持的浏览器和版本。

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    |
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ |
| **呼叫客户端库** | Chrome*、新 Edge | Chrome、Safari* | Chrome*  | Chrome* | Chrome* | Safari** |


*请注意，除前两个版本外，还支持最新版本的 Chrome。<br/>

**请注意，支持 Safari 版本 13.1+。 尚不支持 Safari macOS 的传出视频，但它在 iOS 上受支持。 仅桌面 iOS 支持传出屏幕共享。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [呼叫入门](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

有关详细信息，请参阅以下文章：
- 熟悉常规[呼叫流](../call-flows.md)
- 了解[呼叫类型](../voice-video-calling/about-call-types.md)
- [规划 PSTN 解决方案](../telephony-sms/plan-solution.md)

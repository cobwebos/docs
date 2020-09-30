---
title: 什么是 Azure 通信服务？
description: 了解 Azure 通信服务如何通过实时通信帮助你开发丰富的用户体验。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 1c79ffb02df4a64b2c506ef0863d9bb2da06db53
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91303363"
---
# <a name="what-is-azure-communication-services"></a>什么是 Azure 通信服务？

[!INCLUDE [Public Preview Notice](./includes/public-preview-include.md)]

Azure 通信服务使你可以轻松地将实时多媒体语音、视频和 IP 电话功能添加到应用程序。 通信服务客户端库还使你可以将聊天和短信功能添加到通信解决方案。

<br>

> [!VIDEO https://www.youtube.com/embed/49oshhgY6UQ]

<br>
<br>

可以在各种方案中将通信服务用于语音、视频、文本和数据通信：

- 浏览器到浏览器、浏览器到应用以及应用到应用通信
- 用户与机器人或其他服务交互
- 用户和机器人通过公共电话交换网交互

支持混合方案。 例如，通信服务应用程序的用户可能会同时从浏览器和传统电话设备讲话。 通信服务还可能与 Azure 机器人服务合并，以构建机器人驱动的互动语音响应 (IVR) 系统。

## <a name="common-scenarios"></a>常见方案

如果你不熟悉 Azure 通信服务，则以下资源是很好的起点：
<br>

| 资源                               |说明                           |
|---                                    |---                                   |
|[创建通信服务资源](./quickstarts/create-communication-resource.md)|可以使用 Azure 门户或通信服务管理客户端库预配第一个通信服务资源，从而开始使用 Azure 通信服务。 具有通信服务资源连接字符串后，便可以预配第一个用户访问令牌。|
|[创建第一个用户访问令牌](./quickstarts/access-tokens.md)|用户访问令牌用于针对 Azure 通信服务资源进行服务身份验证。 这些令牌使用通信服务管理客户端库进行预配和重新颁发。|
|[获取电话号码](./quickstarts/telephony-sms/get-phone-number.md)|可以使用 Azure 通信服务预配和发布电话号码。 这些电话号码可以用于启动出站呼叫和构建短信通信解决方案。|
|[从应用发送短信](./quickstarts/telephony-sms/send.md)|Azure 通信服务短信客户端库使你可以从 .NET 和 JavaScript 应用程序发送和接收短信。|
|[语音和视频呼叫入门](./quickstarts/voice-video-calling/getting-started-with-calling.md)| Azure 通信服务使你可以使用呼叫客户端库将语音和视频呼叫添加到应用。 此库由 WebRTC 提供支持，使你可以在应用程序中建立对等的多媒体实时通信。|
|[聊天入门](./quickstarts/chat/get-started.md)|Azure 通信服务聊天客户端库可用于将实时聊天集成到应用程序中。|


## <a name="samples"></a>示例

以下示例演示 Azure 通信服务客户端库的端到端利用。 可随意使用这些示例来启动你自己的通信服务解决方案。
<br>

| 示例名称                               | 描述                           |
|---                                    |---                                   |
|[群呼英雄示例](./samples/calling-hero-sample.md)|了解如何使用通信服务客户端库构建群呼体验。|
|[群聊英雄示例](./samples/chat-hero-sample.md)|了解如何使用通信服务客户端库构建群聊体验。|


## <a name="platforms-and-client-libraries"></a>平台和客户端库

以下资源将帮助你了解 Azure 通信服务客户端库：

| 资源                               | 说明                           |
|---                                    |---                                   |
|[客户端库和 REST API](./concepts/sdk-options.md)|Azure 通信服务功能在概念上分为六个区域，每个区域都由一个客户端库进行表示。 可以基于实时通信需求决定要使用的客户端库。|
|[呼叫客户端库概述](./concepts/voice-video-calling/calling-sdk-features.md)|查看通信服务呼叫客户端库概述。|
|[聊天客户端库概述](./concepts/chat/sdk-features.md)|查看通信服务聊天客户端库概述。|
|[短信客户端概述](./concepts/telephony-sms/sdk-features.md)|查看通信服务短信客户端库概述。|

## <a name="compare-azure-communication-services"></a>比较 Azure 通信服务

你可以考虑利用两个其他 Microsoft 通信产品，它们目前不能直接与通信服务交互：

 - [Microsoft Graph 云通信 API](https://docs.microsoft.com/graph/cloud-communications-concept-overview) 使组织可以构建与具有 M365 许可证的 Azure Active Directory 用户关联的通信体验。 这非常适合与 Azure Active Directory 关联的应用程序，或是要在 Microsoft 团队中扩展工作效率体验的应用程序。 还有一些用于在[团队体验](https://docs.microsoft.com/microsoftteams/platform/?view=msteams-client-js-latest&preserve-view=true)中构建应用程序和自定义的 API。

 - [Azure PlayFab Party](https://docs.microsoft.com/gaming/playfab/features/multiplayer/networking/) 简化了将低延迟聊天和数据通信添加到游戏的过程。 虽然可以使用通信服务来支持游戏聊天和网络系统，不过 PlayFab 是一种定制选项，可在 Xbox 上免费使用。


## <a name="next-steps"></a>后续步骤

 - [创建通信服务资源](./quickstarts/create-communication-resource.md)

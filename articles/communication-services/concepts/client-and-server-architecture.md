---
title: 客户端和服务器体系结构
titleSuffix: An Azure Communication Services concept document
description: 了解通信服务的体系结构。
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: b844f61963081bf355837fd26254915112cbce11
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91666936"
---
# <a name="client-and-server-architecture"></a>客户端和服务器体系结构

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed: 
> - Need to add security best practices for token management here
> - Reference docs:
> - https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/create-a-token-object
> - https://docs.microsoft.com/azure/aks/operator-best-practices-identity
> - https://docs.microsoft.com/cloud-app-security/api-tokens?view=gestures-1.0-->

每个 Azure 通信服务应用程序都有 **客户端应用程序，这些应用程序** 使用 **服务** 来促进用户与人员的连接。 本页说明各种情况下的常见体系结构元素。

## <a name="user-access-management"></a>用户访问管理

Azure 通信服务客户端库需要 `user access tokens` 安全访问通信服务资源。 `User access tokens` 由于令牌的敏感特性和生成所需的连接字符串，应由受信任的服务生成并管理。 如果无法正确管理访问令牌，可能会因为资源被滥用而导致额外的费用。 强烈建议使用受信任的服务进行用户管理。 受信任的服务将生成令牌并使用正确的加密将它们传递回客户端。 下面是一个示例体系结构流：

:::image type="content" source="../media/scenarios/archdiagram-access.png" alt-text="显示用户访问令牌体系结构的关系图。":::

有关其他信息，请查看 [最佳标识管理实践](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices)

## <a name="browser-communication"></a>浏览器通信

Azure 通信 JavaScript 客户端库可以启用具有丰富文本、语音和视频交互功能的 web 应用程序。 应用程序通过客户端库直接与 Azure 通信服务交互，以访问数据平面，并提供实时文本、语音和视频通信。 下面是一个示例体系结构流：

:::image type="content" source="../media/scenarios/archdiagram-browser.png" alt-text="显示用户访问令牌体系结构的关系图。":::

## <a name="native-app-communication"></a>本机应用通信

很多方案都适用于本机应用程序。 Azure 通信服务支持浏览器到应用和应用到应用的通信。  生成本机应用程序体验时，即使在应用程序未运行的情况下，也可以使用推送通知来接收调用。 利用 Azure 通信服务，可以轻松地将集成推送通知发送到 Google Firebase、Apple Push Notification 服务和 Windows 推送通知。 下面是一个示例体系结构流：

:::image type="content" source="../media/scenarios/archdiagram-app.png" alt-text="显示用户访问令牌体系结构的关系图。":::

## <a name="voice-and-sms-over-the-public-switched-telephony-network-pstn"></a>通过公共切换电话网络进行语音和短信 (PSTN) 

通过电话系统进行通信会大幅增加应用程序的覆盖范围。 为了支持 PSTN 语音和短信方案，Azure 通信服务可帮助你直接从 Azure 门户 [获取电话号码](../quickstarts/telephony-sms/get-phone-number.md) ，或者使用 REST api 和客户端库。 获取电话号码后，可以使用这些电话号码在入站和出站方案中同时使用两个 PSTN 调用和 SMS。 下面是一个示例体系结构流：

> [!Note]
> 在公共预览版期间，我们提供了美国和加拿大的帐单地址的客户电话号码的设置。 

:::image type="content" source="../media/scenarios/archdiagram-pstn.png" alt-text="显示用户访问令牌体系结构的关系图。":::

有关 PSTN 和 SMS 解决方案的详细信息，请参阅 [规划你的 pstn 和 sms 解决方案](../concepts/telephony-sms/plan-solution.md)

## <a name="humans-communicating-with-bots-and-other-services"></a>与 bot 和其他服务进行通信的人

Azure 通信服务通过文本和语音频道，使用直接访问 Azure 通信服务数据平面的服务支持人到系统的通信。 例如，你可以让机器人应答来电或参加网络聊天。 Azure 通信服务提供了客户端库，这些库可实现这些方案以便调用和聊天。 下面是一个示例体系结构流：

:::image type="content" source="../media/scenarios/archdiagram-bot.png" alt-text="显示用户访问令牌体系结构的关系图。":::

## <a name="networking"></a>网络

例如，你可能想要在用户之间交换任意数据，例如同步共享混合现实或游戏体验。 可通过两种方式直接使用用于文本、语音和视频通信的实时数据平面：

- **调用客户端库** -调用中的设备可以访问 api，以便通过调用通道发送和接收数据。 这是将数据通信添加到现有交互的最简单方法。
- **STUN/turn** -Azure 通信服务使符合标准的 STUN，并为你提供服务。 这允许您在这些标准化基元之上构建一个高度自定义的传输层。 你可以创建自己的符合标准的客户端或使用开源库，如 [WinRTC](https://github.com/microsoft/winrtc)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建用户访问令牌](../quickstarts/access-tokens.md)

有关详细信息，请参阅下列文章：

- 了解 [身份验证](../concepts/authentication.md)
- 了解 [PSTN 和 SMS 解决方案](../concepts/telephony-sms/plan-solution.md)

- [向应用添加聊天](../quickstarts/chat/get-started.md)
- [添加对应用的音频呼叫](../quickstarts/voice-video-calling/getting-started-with-calling.md)

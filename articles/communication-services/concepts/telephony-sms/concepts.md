---
title: Azure 通信服务中的短信概念
titleSuffix: An Azure Communication Services concept document
description: 了解通信服务短信概念。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e5cfc1e27bae10a1c67e4506afe9db825664785f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943380"
---
# <a name="sms-concepts"></a>短信概念

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

通过 Azure 通信服务，可以使用通信服务短信客户端库来发送和接收短信。 这些客户端库可用于支持客户服务方案、约会提醒、双重身份验证和其他实时通信需求。 通过通信服务短信，可以可靠发送消息，同时还能获得市场活动的可传送性和响应率的相关见解。

Azure 通信服务短信客户端库的主要功能包括：

-  简单设置体验，用于将短信功能添加到应用程序。
- 为美国 A2P（应用程序到个人）用例提供通过免费电话号码实现的高速消息支持。
- 双向会话功能，支持客户支持、警报和约会提醒等方案。
- 可靠交付，同时提供从应用程序发送的消息的实时传送报告。
- 分析功能，可跟踪使用模式和客户参与情况。
- “选择退出”处理支持，自动检测和执行免费号码的选择退出操作。 通信服务检测 STOP 和 START 消息并向最终用户发送以下默认响应： 
  - STOP -“你已成功取消此号码的消息订阅。回复 START 可重新订阅。”
  - START -“你已成功重新订阅此号码的消息。回复 STOP 以取消订阅。”
  - 系统会将 STOP 和 START 消息返回给你。 Azure 通信服务鼓励你监视和实施上述选择退出操作，以确保不再向已选择退出通信的收件人发送消息。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [开始发送短信](../../quickstarts/telephony-sms/send.md)

你可能会对下列文档感兴趣：

- 熟悉[短信客户端库](../telephony-sms/sdk-features.md)
- 获取支持短信的[电话号码](../../quickstarts/telephony-sms/get-phone-number.md)
- [规划短信解决方案](../telephony-sms/plan-solution.md)

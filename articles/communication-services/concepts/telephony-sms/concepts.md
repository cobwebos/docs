---
title: Azure 通信服务中的短信概念
titleSuffix: An Azure Communication Services concept document
description: 了解通信服务短信概念。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 35398d60008ac52ba16dca0a0201f8c2f2101a0f
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758551"
---
# <a name="sms-concepts"></a>短信概念

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

通过 Azure 通信服务，可以使用通信服务短信客户端库来发送和接收短信。 这些客户端库可用于支持客户服务方案、约会提醒、双重身份验证和其他实时通信需求。 通过通信服务短信，可以可靠发送消息，同时还能获得市场活动的可传送性和响应率的相关见解。

Azure 通信服务短信客户端库的主要功能包括：

-  简单设置体验，用于将短信功能添加到应用程序。
- 为美国 A2P（应用程序到个人）用例提供通过免费电话号码实现的高速消息支持。
- 双向会话功能，支持客户支持、警报和约会提醒等方案。
- 可靠交付，同时提供从应用程序发送的消息的实时传送报告。
- 分析功能，可跟踪使用模式和客户参与情况。
- “选择退出”处理支持，自动检测和执行免费号码的选择退出操作。 美国运营商强制实施美国免费号码的选择退出操作。
  - STOP - 如果短信接收者希望选择退出，可以向免费电话号码发送“STOP”。 运营商针对 STOP 发送以下默认响应：“网络消息:你已回复“stop”，这会阻止从该号码发送的所有短信。回复“unstop”可再次接收短信。”
  - START/UNSTOP - 如果接收者希望重新订阅某个免费号码的短信，可以向该免费号码发送“START”或“UNSTOP”。 运营商针对 START/UNSTOP 发送以下默认响应：“网络消息:你已回复“unstop”，将再次从该号码接收短信。”
  - Azure 通信服务将检测到 STOP 消息，并阻止后续向接收者发送的所有短信。 发送报告将指示发送失败，状态消息为“给定接收者已阻止发送者”。
  - 系统会将 STOP、UNSTOP 和 START 消息返回给你。 Azure 通信服务建议你监视和实施上述选择退出操作，以确保不再尝试向已选择退出通信的接收者发送短信。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [开始发送短信](../../quickstarts/telephony-sms/send.md)

你可能会对下列文档感兴趣：

- 熟悉[短信客户端库](../telephony-sms/sdk-features.md)
- 获取支持短信的[电话号码](../../quickstarts/telephony-sms/get-phone-number.md)
- [规划短信解决方案](../telephony-sms/plan-solution.md)

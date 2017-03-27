---
title: "Azure 多重身份验证 - 工作原理"
description: "Azure 多重身份验证可帮助保护对数据和应用程序的访问，同时可以满足用户对简单登录过程的需求。 它通过要求第二种形式的身份验证提供额外的安全性，并通过一系列简单的身份验证选项提供增强式身份验证。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtland
ms.assetid: d14db902-9afe-4fca-b3a5-4bd54b3d8ec5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: e028130569a1e55834f68bde87f21f6f7505d41f
ms.openlocfilehash: 9132e9bde7403e2868b92cb1149904d926ff5d8e
ms.lasthandoff: 02/23/2017


---
# <a name="how-azure-multi-factor-authentication-works"></a>Azure 多重身份验证的工作原理
多因素身份验证的安全性在于它的分层方法。 破坏多因素身份验证系统对于攻击者来说是巨大的挑战。 即使攻击者设法得到用户的密码，如果没有同时占有可信设备也没有用处。 如果用户丢失了设备，捡到该设备的人也无法使用它，除非他（她）也知道该用户的密码。

![验证](./media/multi-factor-authentication-how-it-works/howitworks.png)

Azure Multi-Factor Authentication 可帮助保护对数据和应用程序的访问，同时可以满足用户对简单登录过程的需求。  它通过要求第二种形式的身份验证提供额外的安全性，并通过一系列简单的身份验证选项提供增强式身份验证。

有关其工作原理的详细信息，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Events/TechEd/Europe/2014/EM-B313/player]

## <a name="methods-available-for-two-step-verification"></a>可用于双重验证的方法
当用户登录时，系统会将额外的身份验证发送给该用户。  以下是可用于这种二次身份验证的方法列表。

| 验证方法 | 说明 |
| --- | --- |
| 电话呼叫 |向用户的注册手机拨打电话，要求他们按 # 号或输入 PIN 来验证其登录。 |
| 短信 |将包含&6; 位数代码的短信发送到用户的移动电话。  输入此代码即可完成验证过程。 |
| 移动应用通知 |将验证请求发送到用户的智能手机，要求他们通过在移动应用中选择“验证”来完成验证。 如果你将应用通知选作主要验证方法，则会发生这种情况。  如果用户在未登录时收到通知，他们可以选择将通知举报为欺诈。 |
| 移动应用验证码 |在用户的智能手机上运行的移动应用将显示一个每隔 30 秒钟更改一次的 6 位数字验证码。 用户找到最新代码，并在登录页上输入该代码，以完成验证过程。 如果将验证码选作主要验证方法，则会发生这种情况。 |
| 第三方 OATH 令牌 | 可以将 Azure 多重身份验证配置为接受第三方验证方法。 |

Azure 多重身份验证为云和服务器提供了可选择的验证方法。 这表示可以选择用户可使用的方法：电话呼叫、信息、应用通知或应用代码。 有关更多信息，请参阅[可选择的验证方法](multi-factor-authentication-whats-next.md#selectable-verification-methods)。

## <a name="next-steps"></a>后续步骤

- 阅读有关 [Azure 多重身份验证的不同版本和使用方法](multi-factor-authentication-versions-plans.md)的信息

- 选择是要将 Azure MFA 部署[在云中还是本地](multi-factor-authentication-get-started.md)

---
title: 了解 Azure MFA 中的双重验证 | Microsoft Docs
description: 'Azure 多重身份验证概念、使用 MFA 的原因、有关多重身份验证客户端以及其他可用方法和版本的详细信息。 '
keywords: MFA 简介, mfa 概述, 什么是 mfa
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: c40d7a34-1274-4496-96b0-784850c06e9b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2017
ms.author: joflore
ms.openlocfilehash: 74441b5053a9c7c25d94406293828fe92f210bee
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-azure-multi-factor-authentication"></a>什么是 Azure 多重身份验证？
双重验证是需要使用一个以上验证方法的身份验证方法，为用户登录和事务额外提供一层重要的安全保障。 它需要以下验证方法中的两种或更多种来进行工作：

* 你知道的某样东西（通常为密码）
* 具有的某样东西（无法轻易复制的可信设备，如电话）
* 自身的特征（生物辨识系统）

<center>![用户名和密码](./media/multi-factor-authentication/pword.png)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![证书](./media/multi-factor-authentication/phone.png)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![智能手机](./media/multi-factor-authentication/hware.png)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![智能卡](./media/multi-factor-authentication/smart.png)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![虚拟智能卡](./media/multi-factor-authentication/vsmart.png)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![用户名和密码](./media/multi-factor-authentication/cert.png)</center>

Azure 多重身份验证 (MFA) 是 Microsoft 的双重验证解决方案。 Azure MFA 可帮助保护对数据和应用程序的访问，同时满足用户对简单登录过程的需求。 它通过各种验证方法（包括电话、短信或移动应用验证）提供强大的身份验证机制。

## <a name="why-use-azure-multi-factor-authentication"></a>为何使用 Azure 多重身份验证？
与以往相比，联网的用户越来越多。 使用智能手机、平板电脑、笔记本电脑和台式机，人们可以通过多种方法从任何位置访问他们的帐户和应用程序，并随时保持连接状态。

Azure 多重身份验证是一种易于使用、可缩放且可靠的解决方案，用于提供第二种身份验证方法来保护用户。

| ![易用](./media/multi-factor-authentication/simple.png) | ![可缩放](./media/multi-factor-authentication/scalable.png) | ![始终受保护](./media/multi-factor-authentication/protected.png) | ![可靠](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **易于使用** |**规模可变** |**始终受保护** |**可靠** |

* **易用** - Azure 多重身份验证的设置和使用都很简单。 Azure 多重身份验证自带的额外保护允许用户管理自己的设备。 最重要的是在许多情况下，都只需单击几次便可完成设置。
* **规模可变** - Azure 多重身份验证利用云技术并与本地 AD 和自定义应用相集成。 这种保护甚至可以延伸到高事务量的任务关键型方案。
* **始终受保护** - Azure 多重身份验证使用最高行业标准提供强大的身份验证功能。
* 可靠 - Microsoft 保证 Azure 多重身份验证 99.9% 的可用性。 当服务无法接收或处理双重验证的验证请求时，即将服务视为不可用。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]


## <a name="next-steps"></a>后续步骤

- 了解 [Azure 多重身份验证的工作原理](concept-mfa-howitworks.md)

- 阅读有关 [Azure 多重身份验证的不同版本和使用方法](concept-mfa-licensing.md)的信息

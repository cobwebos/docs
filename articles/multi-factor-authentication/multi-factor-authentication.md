---
title: "了解 Azure MFA 中的双重验证 | Microsoft Docs"
description: "Azure 多重身份验证概念、使用 MFA 的原因、有关多重身份验证客户端以及其他可用方法和版本的详细信息。 "
keywords: "MFA 简介, mfa 概述, 什么是 mfa"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: c40d7a34-1274-4496-96b0-784850c06e9b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 78daa5a75b3414e2761333ea6ad91945596553c8
ms.openlocfilehash: 757a26ed6c39e6eb498a4f717a2eb5da3cb5c0a6


---
# <a name="what-is-azure-multi-factor-authentication"></a>什么是 Azure 多重身份验证？
双重验证是需要使用一个以上验证方法的身份验证方法，为用户登录和事务额外提供一层重要的安全保障。 它需要以下验证方法中的两种或更多种来进行工作：

* 你知道的某样东西（通常为密码）
* 你具有的某样东西（无法轻易复制的可信设备，如电话）
* 你自身的特征（生物辨识系统）

<center>![用户名和密码](./media/multi-factor-authentication/pword.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![证书](./media/multi-factor-authentication/phone.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![智能手机](./media/multi-factor-authentication/hware.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![智能卡](./media/multi-factor-authentication/smart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![虚拟智能卡](./media/multi-factor-authentication/vsmart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![用户名和密码](./media/multi-factor-authentication/cert.png)</center>

Azure 多重身份验证 (MFA) 是 Microsoft 的双重验证解决方案。 Azure MFA 可帮助保护对数据和应用程序的访问，同时满足用户对简单登录过程的需求。 它通过各种验证方法（包括电话、短信或移动应用验证）提供强大的身份验证机制。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/WA-MFA-Overview/player]
>
>

## <a name="why-use-azure-multi-factor-authentication"></a>为何使用 Azure 多重身份验证？
与以往相比，联网的用户越来越多。 通过智能手机、平板电脑、笔记本电脑和台式个人电脑，人们可以使用各种不同的选项随时连接网络和保持联系。 人们可以从任何位置访问帐户与应用程序，这意味着，他们可以提高工作效率并为客户提供更好的服务。

Azure 多重身份验证是一个易于使用、可缩放且可靠的解决方案，可提供另一种身份验证方法，使你的用户永远受到保护。

| ![易用](./media/multi-factor-authentication/simple.png) | ![可缩放](./media/multi-factor-authentication/scalable.png) | ![始终受保护](./media/multi-factor-authentication/protected.png) | ![可靠](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **易于使用** |**规模可变** |**始终受保护** |**可靠** |

* **易用** - Azure 多重身份验证的设置和使用都很简单。 Azure 多重身份验证自带的额外保护允许用户管理自己的设备。 最重要的是在许多情况下，都只需单击几次便可完成设置。
* **规模可变** - Azure 多重身份验证利用云技术并与本地 AD 和自定义应用相集成。 这种保护甚至可以延伸到高事务量的任务关键型方案。
* **始终受保护** - Azure 多重身份验证使用最高行业标准提供强大的身份验证功能。
* **可靠** - 我们保证 Azure 多重身份验证的可用性达到 99.9%。 当服务无法接收或处理双重验证的验证请求时，即将服务视为不可用。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]
>
>

## <a name="how-azure-multi-factor-authentication-works"></a>Azure 多重身份验证的工作原理
双重验证的安全性在于它的分层方法。 破坏多种验证方法对于攻击者来说是巨大的挑战。 即使攻击者设法得到用户的密码，如果没有同时占有可信设备也没有用处。 如果用户丢失设备，捡到该设备的人也无法使用它，除非知道该用户的密码。

> [!VIDEO https://channel9.msdn.com/Events/TechEd/Europe/2014/EM-B313/player]
>
>

## <a name="methods-available-for-multi-factor-authentication"></a>可用于多重身份验证的方法
当用户登录时，系统将额外的验证请求发送给该用户。 以下是可用于这种二次身份验证的方法列表。

| 验证方法 | 说明 |
| --- | --- |
| 电话呼叫 |在用户登录时呼叫用户电话，请求验证。 按 # 键完成验证过程。 此选项可配置，并可以更改为你指定的代码。 |
| 短信 |将包含 6 位数代码的短信发送到用户的智能手机。 输入此代码即可完成验证过程。 |
| 移动应用通知 |将验证请求发送到用户的智能手机，要求他们通过在移动应用中选择“验证”来完成验证。 出现此情况的前提是将应用通知作为主验证方法。 如果用户在未登录时收到通知，可以将通知举报为欺诈。 |
| 移动应用的验证码 |用户设备上的移动应用可生成验证码。 出现此情况的前提是将验证码选作主要验证方法。 |

对于移动应用验证方法，Azure 多重身份验证适用于智能手机的第三方身份验证应用。 但是，建议用户使用 Microsoft Authenticator 应用，该应用可用于 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073)。

## <a name="available-versions-of-azure-multi-factor-authentication"></a>可用的 Azure 多重身份验证版本
Azure 多重身份验证有三个不同的版本。

| 版本 | 说明 |
| --- | --- |
| 适用于 Office 365 的多重身份验证 |此版本专门与 Office 365 应用程序配合使用，可以从 Office 365 门户进行管理。 因此，管理员现在可以使用双重验证来帮助保护其 Office 365 资源。 此版本是 Office 365 订阅的一部分。 |
| 面向 Azure 管理员的多重身份验证 |适用于 Office 365 的同样一部分双重验证功能将免费提供给所有 Azure 管理员使用。 Azure 订阅的每个管理员帐户都可以启用此功能来获得额外保护。 如果某个管理员想要访问 Azure 门户来创建 VM 或网站、管理存储，或使用任何其他 Azure 服务，可以将 MFA 添加到管理员帐户。 |
| Azure 多重身份验证 | Azure 多重身份验证（也称为“完整” 版本）提供了最丰富的功能集。 它通过 [Azure 经典门户](https://manage.windowsazure.com)、高级报告及支持一系列本地和云应用程序来提供其他配置选项。 Azure 多重身份验证随附在 Azure Active Directory Premium 和企业移动套件中，可以在云中或本地部署。 查看以下内容，了解[获取 Azure 多重身份验证的其他方法](multi-factor-authentication.md#how-to-get-azure-multi-factor-authentication)。 |

## <a name="feature-comparison-of-versions"></a>版本功能比较
下表提供了 Azure 多重身份验证的各个版本中可用的功能列表。

> [!NOTE]
> 此比较表讨论了每个版本的多重身份验证的部分功能。 如果拥有完整的 Azure 多重身份验证服务，某些功能可能不可用，具体取决于是否在云中使用 [MFA 或本地 MFA](multi-factor-authentication-get-started.md)。


| 功能 | 适用于 Office 365 的多重身份验证 | 面向 Azure 管理员的多重身份验证 | Azure 多重身份验证 |
| --- |:---:|:---:|:---:|
| 使用 MFA 保护管理员帐户 |● |●（仅适用于 Azure 管理员帐户） |● |
| 将移动应用用作第二个因素 |● |● |● |
| 将电话呼叫用作第二个因素 |● |● |● |
| 将短信用作第二个因素 |● |● |● |
| 不支持 MFA 的客户端的应用密码 |● |● |● |
| 管理员控制验证方法 |● |● |● |
| PIN 模式 | | |● |
| 欺诈警报 | | |● |
| MFA 报告 | | |● |
| 一次性跳过 | | |● |
| 通话的自定义问候语 | | |● |
| 通话的自定义呼叫方 ID | | |● |
| 事件确认 | | |● |
| 受信任的 IP | | |● |
| 记住受信任的设备的 MFA |● |● |● |
| MFA SDK | | |● 需要多重身份验证提供程序和完整的 Azure 订阅 |
| 适用于本地应用程序的 MFA | | |● |

## <a name="how-to-get-azure-multi-factor-authentication"></a>如何获取 Azure 多重身份验证
如果想要获取 Azure 多重身份验证提供的完整功能，可通过多种方法实现：

1. 购买 Azure 多重身份验证许可证并将其分配给用户。
2. 购买随附 Azure 多重身份验证的许可证（例如 Azure Active Directory Premium、企业移动性套件或企业云套件）并将其分配给用户。
3. 在 Azure 订阅中创建 Azure 多重身份验证提供程序。 使用 Azure 多重身份验证提供程序时，有两种使用模式（通过 Azure 订阅计费）可用：  
   * **按用户**。 适用于想要为人数固定、需要定期进行身份验证的员工启用双重验证的企业。  
   * **按身份验证**。 适用于想要为不定期需要身份验证的大量外部用户启用双重验证的企业。  

Azure 多重身份验证为云和服务器提供了可选择的验证方法。 这表示可以选择用户可使用的方法：电话呼叫、信息、应用通知或应用代码。 有关更多信息，请参阅[可选择的验证方法](multi-factor-authentication-whats-next.md#selectable-verification-methods)。

有关定价详细信息，请参阅 [Azure MFA 定价](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。

## <a name="next-steps"></a>后续步骤
若要开始使用 Azure 多重身份验证，第一步是选择[在云中还是在本地使用 MFA](multi-factor-authentication-get-started.md)



<!--HONumber=Jan17_HO5-->



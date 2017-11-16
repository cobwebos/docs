---
title: "云中的 Azure MFA 入门 | Microsoft Docs"
description: "这是 Microsoft Azure 多重身份验证页面，介绍了如何在云中开始使用 Azure MFA。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: richagi
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/24/2017
ms.author: joflore
ms.openlocfilehash: c67ca4b17808c17b40f78a47fe0cc55efd9915bc
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>云中的 Azure 多重身份验证入门
本文介绍如何在云中开始使用 Azure 多重身份验证。

> [!NOTE]
> 以下文档介绍如何通过 **Azure 经典门户**启用用户。 若要了解如何为 O365 用户设置 Azure 多重身份验证，请参阅[为 Office 365 设置多重身份验证](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)。

![云中的 MFA](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisite"></a>先决条件
[注册 Azure 订阅](https://azure.microsoft.com/pricing/free-trial/) - 如果没有 Azure 订阅，则需要注册一个订阅。 对于只是在摸索如何使用 Azure MFA 的新手，可以使用试用版订阅

## <a name="enable-azure-multi-factor-authentication"></a>启用 Azure 多重身份验证
只要用户的许可证包含 Azure 多重身份验证，则不需执行任何操作来启用 Azure MFA。 可以在单个用户的基础上，开始要求进行双重验证。 启用 Azure MFA 的许可证是：
- Azure 多重身份验证
- Azure Active Directory Premium
- 企业移动性 + 安全性

如果这三个许可证你都没有，或者许可证数量不足以涵盖所有用户，则也没有问题。 只需执行一个额外的步骤，即可在目录中[创建多重身份验证提供程序](multi-factor-authentication-get-started-auth-provider.md)。

## <a name="turn-on-two-step-verification-for-users"></a>为用户开启双重验证

执行[如何要求对用户或组进行双重验证](multi-factor-authentication-get-started-user-states.md)中列出的某个过程，开始使用 Azure MFA。 可以选择强制要求所有登录都进行双重验证，也可以创建条件性访问策略，仅在事关重大时要求双重验证。

## <a name="next-steps"></a>后续步骤
在云中设置 Azure 多重身份验证以后，即可配置和设置部署。 请参阅[配置 Azure 多重身份验证](multi-factor-authentication-whats-next.md)了解更多详细信息。


---
title: 外部标识的标识提供者 - Azure AD
description: Azure Active Directory B2B 协作支持多重身份验证 (MFA)，以便对公司应用程序进行选择性访问
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395473e70137ead2b7185d54d165f82e9b3f1f04
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595706"
---
# <a name="identity-providers-for-external-identities"></a>外部标识的标识提供者

标识提供程序创建、维护和管理标识信息，同时向应用程序提供身份验证服务。 与外部用户共享应用和资源时，Azure AD 是用于共享的默认标识提供者。 这意味着，当你邀请已有 Azure AD 或 Microsoft 帐户的外部用户时，这些用户可以自动登录，而无需你进行进一步的配置。

但是，你可以允许用户使用各种标识提供者登录。 例如，你可以与 Azure AD 支持的社交标识提供者（包括 Google 和 Facebook）建立联合。 你还可以与任何支持 SAML 或 WS 联合身份验证协议的外部标识提供者联合。 借助外部标识提供者联合，你可以让外部用户使用其现有的社交或企业帐户登录到你的应用。

## <a name="how-it-works"></a>工作原理

Azure AD 外部标识已预先配置为与 Google 和 Facebook 联合。 若要在 Azure AD 租户中设置这些标识提供者，需针对每个标识提供者创建一个应用程序并配置凭据。 你将获得一个客户端或应用 ID 和一个客户端或应用机密，然后你可以将其添加到 Azure AD 租户。

将标识提供者添加到 Azure AD 租户后：

- 当你邀请外部用户访问组织中的应用或资源时，外部用户可以使用自己的帐户通过该标识提供者登录。
- 当你为应用启用[自助注册](self-service-sign-up-overview.md)时，外部用户可以使用自己的帐户通过你添加的标识提供者注册你的应用。 

外部用户在兑换你的邀请或注册你的应用时，可以选择通过社交标识提供者进行登录和身份验证：

![屏幕截图，显示带有 Google 和 Facebook 选项的登录屏幕](media/identity-providers/sign-in-with-social-identity.png)

为了获得最佳的登录体验，请尽可能与标识提供者联合，以便你的受邀来宾在访问你的应用时能够获得无缝的登录体验。  

## <a name="next-steps"></a>后续步骤

若要了解如何向应用程序中添加用于登录的标识提供者，请参阅以下文章：

- 向社交标识提供者列表[添加 Google](google-federation.md)
- 向社交标识提供者列表[添加 Facebook](facebook-federation.md)
- 与其标识提供者支持 SAML 2.0 或 WS 联合身份验证协议的任何组织[建立直接联合](direct-federation.md)。 请注意，直接联合不是自助注册用户流的选项。

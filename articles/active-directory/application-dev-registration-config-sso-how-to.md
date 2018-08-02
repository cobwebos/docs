---
title: 如何配置新的多租户应用程序 | Microsoft Docs
description: 如何为正在使用 Azure AD 开发和注册的自定义应用程序配置单一登录。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 97c698fa99c22b689b5e10b91c87345f4eadd647
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365220"
---
# <a name="how-to-configure-a-new-multi-tenant-application"></a>如何配置新的多租户应用程序

通过适用于 OpenID Connect 的 Azure AD、SAML 2.0 或 WS-Fed 联合时，会自动启用应用中的联合单一登录 (SSO)。 如果与 Azure AD 已有一个现有会话，但是最终用户仍然需要登录，这有可能是应用配置错误。

* 如果使用 ADAL/MSAL，请确保将 **PromptBehavior** 设置为“自动”而不是“始终”。

* 如果生成移动应用，可能需要进行额外配置以启用中转或非中转 SSO。

对于 Android 系统，请参阅[在 Android 中启用跨应用 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)。<br>

对于 iOS 系统，请参阅[在 iOS 中启用跨应用 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)。

## <a name="next-steps"></a>后续步骤

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[在 Android 中启用跨应用 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[在 iOS 中启用跨应用 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[将应用与 AzureAD 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[同意并为 AzureAD v2.0 聚合应用授予权限](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)

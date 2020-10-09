---
author: mmacy
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 07/27/2020
ms.author: marsma
ms.openlocfilehash: 99ca8e45da27f5bce5258d55f46bcfa25b358239
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87311529"
---
> [!IMPORTANT]
> MSAL.js 2.0 当前不支持配合使用 Azure AD B2C 和 PKCE 授权代码流。 目前，Azure AD B2C 建议使用 [教程：注册应用程序][implicit-flow]中所述的隐式流。 要跟踪此问题的进展，请参阅 [MSAL.js Wiki][msal-wiki]。

[github-issue]: https://github.com/AzureAD/microsoft-authentication-library-for-js/issues/1795
[implicit-flow]: ../articles/active-directory-b2c/tutorial-register-applications.md
[msal-wiki]: https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-browser-B2C-CORS-issue

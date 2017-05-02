---
title: "如何为给定 API 选择权限 | Microsoft Docs"
description: "如何查找要开发或向 Azure AD 注册的自定义应用程序的身份验证终结点。"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 3578684ac8c92ff195b19740b28ef9adebdc7309
ms.lasthandoff: 04/14/2017


---

# <a name="how-to-select-permissions-for-a-given-api"></a>如何为给定 API 选择权限

可以在 [Azure 门户](https://portal.azure.com)中找到应用程序的身份验证终结点。

-   导航到 [Azure 门户](https://portal.azure.com)。

-   在左侧导航窗格中，单击“Azure Active Directory”。

-   单击“应用注册”并选择“终结点”。

-   这将打开“终结点”页，该页会列出租户的所有身份验证终结点。

-   将特定于要使用的身份验证协议的终结点与应用程序 ID 结合使用，生成特定于应用程序的身份验证请求。

## <a name="next-steps"></a>后续步骤
[Azure Active Directory 开发人员指南](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)


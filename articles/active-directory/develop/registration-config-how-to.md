---
title: 如何为给定 API 选择权限 | Microsoft Docs
description: 如何使用 Azure AD 查找要开发或注册的自定义应用程序的身份验证终结点。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: d9db2182c20663cc048a5c9501df2586db25d2f3
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364925"
---
# <a name="how-to-configure-endpoints"></a>如何配置终结点

可以在 [Azure 门户](https://portal.azure.com)中找到应用程序的身份验证终结点。

-   导航到 [Azure 门户](https://portal.azure.com)。

-   在左侧导航窗格中，单击“Azure Active Directory”。

-   单击“应用注册”并选择“终结点”。

-   这会打开“终结点”页，该页会列出租户的所有身份验证终结点。

-   将特定于要使用的身份验证协议的终结点与应用程序 ID 结合使用，生成特定于应用程序的身份验证请求。

## <a name="next-steps"></a>后续步骤
[Azure Active Directory 开发人员指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)

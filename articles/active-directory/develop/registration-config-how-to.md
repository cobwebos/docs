---
title: 如何为给定 API 选择权限 | Microsoft Docs
description: 如何使用 Azure AD 查找要开发或注册的自定义应用程序的身份验证终结点。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a0962e5381a6e0a8da96bc7379c38aae73ee400
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207018"
---
# <a name="how-to-configure-endpoints"></a>如何配置终结点

可以在 [Azure 门户](https://portal.azure.com)中找到应用程序的身份验证终结点。

-   导航到 [Azure 门户](https://portal.azure.com)。

-   在左侧导航窗格中，单击“Azure Active Directory”。

-   单击“应用注册”并选择“终结点”。

-   这会打开“终结点”页，该页会列出租户的所有身份验证终结点。

-   将特定于要使用的身份验证协议的终结点与应用程序 ID 结合使用，生成特定于应用程序的身份验证请求。

## <a name="next-steps"></a>后续步骤
[Azure Active Directory 开发人员指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

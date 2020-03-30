---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184331"
---
若要在 Azure AD B2C 租户中注册应用程序，可以使用当前的“应用程序”体验，**** 或者使用我们新推出的统一“应用注册(预览版)”体验。**** [了解更多关于新体验](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[应用程序](#tab/applications/)

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录****。
1. 在 Azure 门户中，搜索并选择“Azure Active Directory”。****
1. 在“管理”**** 下，选择“应用注册(旧版)”****
1. 选择“新建应用程序注册”****。
1. 输入应用程序的名称。 例如，*managementapp1*。
1. 对于“应用程序类型”，请选择“Web 应用/API”********。
1. 在“登录 URL”中输入任何有效的 URL。**** 例如，`https://localhost` 。 此终结点不需要可访问，但必须是有效的 URL。
1. 选择 **“创建”**。
1. 记下**已注册的应用**“概述”页上显示的**应用程序 ID**。 在稍后的步骤中会使用此值。

#### <a name="app-registrations-preview"></a>[应用注册（预览版）](#tab/app-reg-preview/)

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录****。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”****。
1. 选择“应用注册(预览版)”，然后选择“新建注册”********。
1. 输入应用程序的“名称”****。 例如，*managementapp1*。
1. 选择“仅此组织目录中的帐户”****。
1. 在“权限”下，清除“授予对 openid 和 office_access 权限的管理员许可”复选框******。
1. 选择“注册”****。
1. 记下应用“概述”页上显示的“应用程序(客户端) ID”****。 在稍后的步骤中会使用此值。
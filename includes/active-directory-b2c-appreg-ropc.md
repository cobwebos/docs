---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 03329252c0ed4231585d1717d9361a2aef35b36f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186978"
---
若要在 Azure AD B2C 租户中注册应用程序，可以使用当前的“应用程序”体验，或者使用我们新推出的统一“应用注册(预览版)”体验。 [详细了解此新体验](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[应用程序](#tab/applications/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 选择“应用程序”，然后选择“添加”。
1. 输入应用程序的名称。 例如， *ROPC_Auth_app*。
1. 对于“本机客户端”，请选择“是”。
1. 保留其他值，然后选择 "**创建**"。
1. 记录**应用程序 ID**，以便在以后的步骤中使用。

#### <a name="app-registrations-preview"></a>[应用注册（预览版）](#tab/app-reg-preview/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 选择“应用注册(预览版)”，然后选择“新建注册”。
1. 输入应用程序的“名称”。 例如， *ROPC_Auth_app*。
1. 保留其他值，然后选择 "**注册**"。
1. 记录“应用程序(客户端) ID”，以便在后续步骤中使用。
1. 在“管理”下，选择“身份验证”。
1. 选择“尝试新体验”（如果已显示）。
1. 在 "**默认客户端类型**" 下，选择 **"是"** 以将应用程序视为公共客户端。 此设置是 ROPC 流所必需的。
1. 选择“保存”。
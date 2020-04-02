---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: cea3245176e6c38137d68e3ad4b47477bedc78be
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529166"
---
若要在 Azure AD B2C 租户中注册应用程序，可以使用当前的“应用程序”体验，**** 或者使用我们新推出的统一“应用注册(预览版)”体验。**** [了解更多关于新体验](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[应用程序](#tab/applications/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”**** 筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”****。 或者，选择“所有服务”**** 并搜索并选择“Azure AD B2C”****。
1. 选择 **"应用程序**"，然后选择 **"添加**"。
1. 输入应用程序的名称。 例如，*ROPC_Auth_app*。
1. 对于“本机客户端”，请选择“是”********。
1. 保留其他值不变，然后选择“创建”****。
1. 记录**应用程序 ID**，以便在以后的步骤中使用。

#### <a name="app-registrations-preview"></a>[应用注册（预览版）](#tab/app-reg-preview/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”**** 筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”****。 或者，选择“所有服务”**** 并搜索并选择“Azure AD B2C”****。
1. 选择“应用注册(预览版)”，然后选择“新建注册”********。
1. 输入应用程序的“名称”****。 例如，*ROPC_Auth_app*。
1. 保留其他值，然后选择 **"注册**"。
1. 记录“应用程序(客户端) ID”，以便在后续步骤中使用****。
1. 在“管理”下，选择“身份验证”。********
1. 选择“尝试新体验”（如果已显示）。****
1. 在 **"默认客户端类型"** 下，选择 **"是**"将应用程序视为公共客户端。 ROPC 流需要此设置。
1. 选择“保存”。 
1. 在左侧菜单中，选择 **"清单"** 以打开清单编辑器。 
1. 将**auth2Allow 隐式Flow**属性设置为*true*：
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. 选择“保存”。 
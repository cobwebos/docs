---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9367719b1a1050ce8abf36804b3d0d0d58c7484b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183359"
---
Web API 资源需要先在租户中注册，然后才能接受并响应通过提供访问令牌的客户端应用程序所提出的受保护资源请求。

若要在 Azure AD B2C 租户中注册应用程序，可以使用当前的“应用程序”体验，  或者使用我们新推出的统一“应用注册(预览版)”体验。  [详细了解此新体验](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[应用程序](#tab/applications/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”  筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”  。 或者，选择“所有服务”  并搜索并选择“Azure AD B2C”  。
1. 选择“应用程序”，然后选择“添加”   。
1. 输入应用程序的名称。 例如，“webapi1”  。
1. 对于“Web 应用/Web API”，请选择“是”。  
1. 对于**允许隐式流**，选择“是”  。
1. 对于“回复 URL”，请输入 Azure AD B2C 要将应用程序请求的任何令牌返回到的终结点  。 本教程中的示例在本地运行并在 `https://localhost:5000` 上进行侦听。
1. 对于“应用 ID URI”，请在所示的 URI 中添加一个 API 终结点标识符。  对于本教程，请输入 `api`，因此，完整的 URI 类似于 `https://contosob2c.onmicrosoft.com/api`。
1. 选择“创建”  。
1. 记录**应用程序 ID**，以便在以后的步骤中使用。

#### <a name="app-registrations-preview"></a>[应用注册（预览版）](#tab/app-reg-preview/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”  筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”  。 或者，选择“所有服务”  并搜索并选择“Azure AD B2C”  。
1. 选择“应用注册(预览版)”，然后选择“新建注册”   。
1. 输入应用程序的“名称”  。 例如，“webapi1”  。
1. 在“重定向 URI”下选择“Web”，然后输入 Azure AD B2C 会将应用程序请求的任何令牌返回到其中的终结点  。  本教程中的示例在本地运行并在 `http://localhost:5000` 上进行侦听。
1. 选择“注册”  。
1. 记录“应用程序(客户端) ID”，以便在后续步骤中使用  。

接下来，启用隐式授权流：

1. 在“管理”下，选择“身份验证”。  
1. 选择“尝试新体验”（如果已显示）。 
1. 在“隐式授权”下，选中“访问令牌”和“ID 令牌”复选框    。
1. 选择“保存”。 
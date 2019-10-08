---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/30/2019
ms.author: marsma
ms.openlocfilehash: 4ac69b51e69031a1a4ce83a2c09dc6cc010ccd62
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720276"
---
Web API 资源需要先在租户中注册，然后才能接受并响应通过提供访问令牌的客户端应用程序所提出的受保护资源请求。

1. 登录到 [Azure 门户](https://portal.azure.com)。
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

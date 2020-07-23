---
title: 将自定义属性添加到自助服务注册流-Azure AD
description: 了解如何自定义自助注册用户流的属性。
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a186b682a3a506f0f373776dd66e6592bc6036ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85551164"
---
# <a name="define-custom-attributes-for-user-flows-preview"></a>定义用户流的自定义属性（预览）

> [!NOTE]
> 自定义用户属性功能是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

对于每个应用，你可能对要在注册期间收集的信息有不同的要求。 Azure AD 随附了一组存储在属性中的内置信息，如名字、姓氏、城市和邮政编码。 使用 Azure AD，可以扩展在外部用户通过用户流注册时存储在来宾帐户中的属性集。

可以在 Azure 门户中创建自定义属性，并将它们用于自助注册用户流。 还可以使用 [Microsoft Graph API](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api) 读写这些属性。 Microsoft Graph API 支持创建用户，并使用扩展属性来更新用户。 图形 API 中的扩展属性使用约定 `extension_<extensions-app-id>_attributename` 来命名。 例如：

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

`<extensions-app-id>`特定于你的租户。 若要查找此标识符，请导航到 Azure Active Directory > 应用注册 > 所有应用程序 "。 搜索以 "aad-extension-应用" 开头的应用并将其选中。 在应用的 "概述" 页上，记下应用程序（客户端） ID。

## <a name="create-a-custom-attribute"></a>创建自定义属性

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，选择“外部标识”。
4. 选择“自定义用户属性(预览)”。 此时会列出可用的用户属性。

   ![选择注册用户流的用户属性](media/user-flow-add-custom-attributes/user-attributes.png)

5. 若要添加属性，请选择“添加”。
6. 在“添加属性”窗格中，输入以下值：

   - 名称 - 输入自定义属性的名称（例如，“Shoesize”）。
   - 数据类型 - 选择数据类型（“字符串”、“布尔”或“整数”）。
   - 说明 -（可选）输入自定义属性的说明，以供内部使用。 此说明对用户不可见。

   ![添加属性](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. 选择“创建”。

现在，自定义属性显示在用户属性列表中，并可用于用户流。 自定义属性只在第一次在任何用户流中使用时创建，而不是在添加到用户属性列表时创建。

通过使用新创建的自定义属性的用户流来新建用户后，可以在 [Microsoft Graph 资源管理器](https://developer.microsoft.com/graph/graph-explorer)中查询对象。 现在，应会在用户对象上注册旅程收集的属性列表中看到**ShoeSize** 。 可以从应用程序调用图形 API，以便在将此特性添加到用户对象之后，从此特性获取数据。

## <a name="next-steps"></a>后续步骤

[向应用添加自助注册用户流](self-service-sign-up-user-flow.md)

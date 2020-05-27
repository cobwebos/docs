---
title: 添加自助注册用户流 - Azure AD
description: 与 Facebook 联合，让外部用户（来宾）能够使用自己的 Facebook 帐户登录 Azure AD 应用。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfd430d750b2220882479a430322f4b4c4e0c44c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594726"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app-preview"></a>向应用添加自助注册用户流（预览）
|     |
| --- |
| 自助注册是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

通过将用户流与应用关联，可以在相应应用上启用注册。 可以选择多个与用户流关联的应用。 在你将用户流与一个或多个应用关联后，访问相应应用的用户就能使用用户流中配置的选项进行注册。

## <a name="before-you-begin"></a>开始之前

### <a name="add-social-identity-providers-optional"></a>添加社交标识提供者（可选）

Azure AD 是自助注册的默认标识提供者。 也就是说，在默认情况下，用户可以使用 Azure AD 帐户进行注册。 也可以通过在这些注册流中添加社交标识提供者来支持 Google 和 Facebook 帐户。

- [向社交标识提供者列表添加 Google](google-federation.md)
- [向社交标识提供者列表添加 Facebook](facebook-federation.md)
 
### <a name="define-custom-attributes-optional"></a>定义自定义属性（可选）

用户属性是在自助注册期间收集的用户值。 Azure AD 随附了一组内置属性，但你也可以创建自定义属性，以用于用户流。 还可以使用 Microsoft Graph API 读写这些属性。 请参阅[定义用户流的自定义属性](user-flow-add-custom-attributes.md)。

## <a name="enable-self-service-sign-up-for-your-tenant"></a>为租户启用自助注册

必须先为租户启用自助注册功能，然后才能向应用添加自助注册用户流。 启用后，用户流中的控件就可用了，可以将用户流与应用关联起来。

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 选择“用户设置”，然后在“外部用户”下，选择“管理外部协作设置”。
4. 将“启用通过用户流进行来宾自助注册(预览)”切换设置为“是”。

   ![启用来宾自助注册](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)

## <a name="create-the-user-flow-for-self-service-sign-up"></a>创建自助注册用户流

接下来，你将创建自助注册用户流，并将它添加到应用中。

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，选择“外部标识”。
4. 依次选择“用户流(预览)”和“新建用户流”。

   ![“添加新用户流”按钮](media/self-service-sign-up-user-flow/new-user-flow.png)

5. 在“创建”页上的“名称”中，输入用户流的名称。 请注意，名称自动以 B2X_1_ 为前缀。
6. 在“标识提供者”列表中，选择一个或多个可供外部用户用来登录应用的标识提供者。 默认选择的是“Azure Active Directory 注册”。 （若要了解如何添加标识提供者，请参阅本文前面的[开始之前](#before-you-begin)。）
7. 在“用户属性”下，选择要收集的用户属性。 若要选择其他属性，请选择“显示更多”。 例如，选择“显示更多”，然后选择“国家/地区”、“显示名称”和“邮政编码”的属性和声明。 选择“确定”。

   ![“新建用户流”页](media/self-service-sign-up-user-flow/create-user-flow.png)

8. 选择“创建”。
9. 此时，新用户流显示在“用户流(预览)”列表中。 如果需要，请刷新页面。

## <a name="select-the-layout-of-the-attribute-collection-form"></a>选择“属性集合”窗体的布局

可以选择属性在注册页上显示的顺序。 

1. 在 [Azure 门户](https://portal.azure.com)中，选择“Azure Active Directory”。
2. 依次选择“外部标识”和“用户流(预览)”。
3. 从列表中选择自助注册用户流。
4. 在“自定义”下选择“页面布局”。 
5. 此时列出你选择收集的属性。 若要更改显示顺序，请选择一个属性，然后选择“上移”、“下移”、“移至顶部”或“移至底部”。
6. 选择“保存”。

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>将应用添加到自助注册用户流

现在，可以关联应用与用户流。

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，选择“外部标识”。
4. 在“自助注册”下，选择“用户流(预览)”。
5. 从列表中选择自助注册用户流。
6. 在左侧菜单中的“使用”下，选择“应用”。
7. 选择“添加应用程序”。

   ![将应用分配到用户流](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. 从列表中选择应用。 或者，使用搜索框来查找应用，然后选择它。
9. 单击“选择”。

## <a name="next-steps"></a>后续步骤

- [向社交标识提供者列表添加 Google](google-federation.md)
- [向社交标识提供者列表添加 Facebook](facebook-federation.md)

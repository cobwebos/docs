---
title: "自定义 Azure Active Directory 中的登录页 | Microsoft Docs"
description: "了解如何在 Azure 登录页中添加公司品牌元素"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 27590c018ea55e9793246c7a4cab10f934ea502b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/05/2017


---
# <a name="add-company-branding-to-your-sign-in-page-in-the-azure-active-directory"></a>在 Azure Active Directory 登录页中添加公司品牌元素
许多公司想要在他们管理的所有网站和服务上应用一致的外观，以免用户感到困惑。 Azure Active Directory 提供了此功能，允许用户使用自己的公司徽标和自定义的颜色方案来自定义登录页外观。 登录页是登录到 Office 365 或其他使用 Azure AD 作为标识提供者的基于 Web 的应用程序时显示的页面。 用户与此页进行交互以输入其凭据。

如果你要在此页上显示你公司的品牌、颜色和其他可自定义元素，请参阅下图以了解两种体验之间的差异。

以下屏幕截图显示自定义 **之前** 台式机上的 Office 365 登录页示例：

![自定义之前的 Office 365 登录页](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

以下屏幕截图显示自定义 **之后** 台式机上的 Office 365 登录页示例：

![自定义之后的 Office 365 登录页](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)

## <a name="customizing-the-sign-in-page"></a>自定义登录页
通常，如果你需要通过浏览器访问组织订阅的云应用和服务，可以使用登录页。

如果你已对登录页应用了更改，最多需要一小时更改才会出现。

仅当使用租户特定的 URL（例如 https://outlook.com/**contoso**.com 或 https://mail.**contoso**.com）访问服务时，才显示带有品牌的登录页。

当使用非租户特定的 URL（例如：https://mail.office365.com）访问服务时，会显示不带品牌的登录页。 在此情况下，输入你的用户 ID 或选择用户磁贴之后，品牌就会出现。

> [!NOTE]
> * 用户的域名在已配置品牌元素的 Azure 门户的“域”部分必须显示为“活动”。 有关详细信息，请参阅[添加自定义域名](active-directory-domains-add-azure-portal.md)。
> * 登录页品牌不会带到 Microsoft 的使用者登录页。 如果使用 Microsoft 帐户登录，则可以看到 Azure AD 呈现的经过品牌打造的用户磁贴列表，但用户的组织的品牌元素未应用于 Microsoft 帐户登录页。
>
>

在登录页中，“使我保持登录状态”复选框允许用户在关闭和重新打开浏览器时保持登录状态。

   ![使我保持登录状态](./media/active-directory-branding-custom-signon-azure-portal/01.png)

它不会影响会话生存期。 可以隐藏 Azure Active Directory 登录页上的此复选框。
是否显示此复选框取决于“使我保持登录状态”的设置。

   ![使我保持登录状态](./media/active-directory-branding-custom-signon-azure-portal/02.png)

若要隐藏此复选框，可将此设置配置为“是”。

> [!NOTE]
> SharePoint Online 和 Office 2010 的某些功能取决于用户能否勾选此框。 如果将此设置配置为“隐藏”，用户可能会在登录时看到其他意外的提示。
>
>

**将公司品牌元素添加到目录：**

1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“更多服务”，在文本框中输入“用户和组”，然后选择 **Enter**。

   ![打开“用户管理”](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)
3. 在“用户和组”边栏选项卡中，选择“公司品牌元素”。
4. 在“用户和组 - 公司品牌元素”边栏选项卡上，选择“编辑”命令。

    ![编辑自定义品牌元素](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)
5. 修改要自定义的元素。 所有元素都是可选的。
6. 单击“保存” 。

最长可能需要一个小时才能显示用户对登录页的品牌元素所做的任何更改。

## <a name="next-steps"></a>后续步骤
[添加特定于语言的公司品牌元素](active-directory-branding-localize-azure-portal.md)


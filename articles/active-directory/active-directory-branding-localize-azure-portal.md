---
title: "在 Azure Active Directory 的登录页中添加特定于语言的公司品牌元素 | Microsoft Docs"
description: "了解如何在 Azure 登录页中添加特定于语言的公司品牌图片和文本"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a0310d6a-aaa7-4ea0-991d-6d3135b4382a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: e1fe8d855386ceec39edbc985538cdf32d78a13b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/05/2017


---
# <a name="add-language-specific-company-branding-to-your-sign-in-page-in-the-azure-active-directory"></a>在 Azure Active Directory 的登录页中添加特定于语言的公司品牌元素
许多公司想要在他们管理的所有网站和服务上应用一致的外观，以免用户感到困惑。 Azure Active Directory 提供了此功能，允许用户使用自己的公司徽标和自定义的颜色方案来自定义登录页外观。 登录页是登录到 Office 365 或其他使用 Azure AD 作为标识提供者的基于 Web 的应用程序时显示的页面。 用户与此页进行交互以输入其凭据。

## <a name="customizing-the-sign-in-page-for-another-language"></a>自定义另一种语言的登录页面
仅在按照[在登录页中添加公司品牌元素](active-directory-branding-custom-signon-azure-portal.md)中所述创建了自定义登录页时，才可以在自定义登录页面中添加特定于语言的元素。 可以使用一组默认的可自定义元素为每个目录配置一个登录页。 在配置完默认的页面元素集后，便可以针对不同的区域设置配置其他版本。 你还可以混搭各种元素。 例如，你可以：

* 创建适用于所有区域设置的默认的**登录页面图**，然后为英文和法文创建特定版本。 将浏览器设置为这两种语言之一时，将出现特定于语言的图像，对于其他所有语言，则显示默认插图。
* 为组织配置不同的徽标（例如日语或希伯来语版本）。

出于维护和性能原因，我们建议你保持较少的语言版本。

**将公司品牌元素添加到目录：**

1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“更多服务”，在文本框中输入“用户和组”，然后选择 **Enter**。

   ![打开“用户管理”](./media/active-directory-branding-localize-azure-portal/user-management.png)
3. 在“用户和组”边栏选项卡中，选择“公司品牌元素”。
4. 在“用户和组 - 公司品牌元素”边栏选项卡上，选择“添加语言”命令。

    ![添加特定于语言的品牌元素](./media/active-directory-branding-localize-azure-portal/add-language.png)
5. 修改要自定义的元素。 所有元素都是可选的。
6. 单击“保存” 。

最长可能需要一个小时才能显示用户对登录页的品牌元素所做的任何更改。

## <a name="next-steps"></a>后续步骤
[在登录页中添加公司品牌元素](active-directory-branding-custom-signon-azure-portal.md)


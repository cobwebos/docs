---
title: "注册 Azure Active Directory Premium | Microsoft Docs"
description: "介绍如何注册 Azure Active Directory Premium Edition"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
ms.assetid: 391764e5-c4eb-4ae6-b8a9-3d09f0de04a6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: it-pro;
ms.openlocfilehash: e7aa1757ec1720f15ac9d1f00129fd38f86f0668
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="quickstart-sign-up-for-azure-active-directory-premium"></a>快速入门：注册 Azure Active Directory Premium
若要开始使用 Azure Active Directory (Azure AD) Premium，可以购买许可证并将其与 Azure 订阅相关联。 如果创建新的 Azure 订阅，则还需激活许可计划和 Azure AD 服务访问权限，如以下部分所述。 

## <a name="sign-up-for-active-directory-premium"></a>注册 Active Directory Premium
若要注册 Active Directory Premium，可以使用多个选项： 
* 使用 Azure 或 Office 365 订阅
* 使用企业移动性 + 安全性许可计划
* 使用 Microsoft 批量许可计划

### <a name="azure-or-office-365"></a>Azure 或 Office 365 
Azure 或 Office 365 订户可以在线购买 Azure Active Directory Premium。 

有关详细步骤，请参阅 [How to Purchase Azure Active Directory Premium - Existing Customers](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer)（如何购买 Azure Active Directory Premium - 现有客户）或 [How to Purchase Azure Active Directory Premium - New Customers](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers)（如何购买 Azure Active Directory Premium - 新客户）。  

### <a name="enterprise-mobility--security"></a>企业移动性 + 安全性
企业移动 + 安全性 (EMS) 提供一种经济高效的方式，让组织可以在一个许可计划下同时使用以下服务：Azure Active Directory Premium、Azure 信息保护和 Microsoft Intune。 若要详细了解 EMS，可参阅[企业移动性 + 安全性网站](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)；若要详细了解可供购买的 EMS 许可证类型，可参阅[企业移动性 + 安全性定价选项](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing)页。  

选择以下许可选项之一即可通过 EMS 许可证开始使用 Azure AD：

- 通过免费的[企业移动性 + 安全性 E5 试用订阅](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1)试用 EMS
- 购买[企业移动性 + 安全性 E5 许可证](https://signup.microsoft.com/Signup?OfferId=e6de2192-536a-4dc3-afdc-9e2602b6c790&ali=1)
- 购买[企业移动性 + 安全性 E3 许可证](https://signup.microsoft.com/Signup?OfferId=4BBA281F-95E8-4136-8B0F-037D6062F54C&ali=1)

### <a name="microsoft-volume-licensing"></a>Microsoft 批量许可
可以通过 [Microsoft 企业协议](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx)（250 个或更多个许可证）或[开放批量许可](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx)（5-250 个许可证）计划获取 Azure Active Directory Premium。

可以在 [How to purchase through Volume Licensing](https://www.microsoft.com/licensing/how-to-buy/how-to-buy.aspx)（如何通过批量许可进行购买）页上了解有关批量许可购买选项的更多信息。

> [!NOTE]
> 在中国，使用 Azure Active Directory 全球实例的客户可以使用 Azure Active Directory 高级和基本版。 由中国 21Vianet 运营的 Microsoft Azure 服务目前不支持 Azure Active Directory 高级和基本版。 有关详细信息，请在 [Azure Active Directory 论坛](https://feedback.azure.com/forums/169401-azure-active-directory/)与我们联系。

如果你此前针对 Azure 订阅购买和激活了 Azure AD 许可证，而该订阅正是你在前述步骤中使用过的订阅，则会在同一目录中自动激活这些许可证。 如果不是，请继续执行本文其余部分介绍的步骤。

## <a name="activate-your-license-plan"></a>激活许可计划
这是否是你从 Microsoft 购买的第一个 Azure AD 许可计划？ 如果是，系统会在你完成购买时生成一封确认电子邮件并将其发送给你。 必须通过这封电子邮件才能激活第一个许可计划。

**要激活许可计划，请执行以下步骤之一：**

1. 若要开始激活，请单击“登录”或“注册”。
   
    ![登录][1]

    - 如果已有现有的租户，请单击“登录”以使用现有的管理员帐户登录。 在必须激活许可证的租户中使用全局管理员凭据登录。

    - 如果要创建新的可与许可计划搭配使用的 Azure AD 租户，请单击“注册”打开“创建帐户配置文件”对话框。

        ![创建帐户配置文件][2]

完成时会显示下列对话框，以确认激活租户的许可计划：

![确认][3]

## <a name="activate-your-azure-active-directory-access"></a>激活 Azure Active Directory 访问权限
如果是向现有的订阅添加新的 Azure AD Premium 许可证，则应该已激活 Azure AD 访问权限。 否则，需在收到欢迎电子邮件后激活 Azure AD 访问权限。  

如果已在目录中预配购买的许可证，系统会向你发送欢迎电子邮件。 此电子邮件确认你可以开始管理 Azure Active Directory Premium 或企业移动性 + 安全性的许可证和功能。 

> [!TIP]
> 在使用欢迎电子邮件激活 Azure AD 目录访问权限之前，不能访问新租户的 Azure AD，该电子邮件是在完成许可证预配过程以后，由系统自动发送的。 

若要激活 Azure AD 访问权限，请执行以下步骤：

1. 在**欢迎电子邮件**中单击“登录”。 
   
    ![欢迎电子邮件][4]
2. 成功登录以后，还需使用移动设备完成双重身份验证：
   
    ![手机验证][5]

激活只需几分钟，然后即可获得管理 Azure AD 的访问权限。 

## <a name="next-steps"></a>后续步骤
本快速入门介绍了如何注册 Azure AD Premium 并激活 Azure Active Directory 访问权限。 

如果已经有了 Azure 订阅，则可通过以下链接开始试用，或者从 Azure 门户购买 Azure AD Premium 许可证。

> [!div class="nextstepaction"]
> [激活 Azure AD Premium 许可证](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/TryBuyProductBlade) 

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png
---
title: 教程：对 Azure AD B2B 来宾用户强制执行多重身份验证
description: 了解如何在使用 Azure AD B2B 与外部用户和合作伙伴组织协作时要求进行多重身份验证 (MFA)。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 34ca5667b5bf47bec8c48a7e5836b7d51ad08724
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "45986811"
---
# <a name="tutorial-enforce-multi-factor-authentication-for-b2b-guest-users"></a>教程：对 B2B 来宾用户强制执行多重身份验证

在与 B2B 来宾用户协作时，最好使用多重身份验证 (MFA) 策略保护你的应用。 实施后，用户在访问你的资源时，不仅仅是要提供用户名和密码。 在 Azure Active Directory (Azure AD) 中，可通过要求 MFA 访问验证的条件访问策略实现此目标。 可在租户、应用或个人来宾用户级别上强制实施 MFA 策略，操作方式与为你自己的组织成员启用这些策略的方式相同。

示例：

![要求 MFA 的 B2B 应用](media/tutorial-mfa/aad-b2b-mfa-example.png)

1.  公司 A 的某位管理员或员工邀请一名来宾用户使用云或本地应用程序，而此程序被配置为要求进行 MFA 访问验证。
2.  该来宾用户使用其自己的工作、学校或社交标识进行登录。 
3.  系统要求该用户完成 MFA 验证。 
4.  该用户向公司 A 设置 MFA，并选择其 MFA 选项。 该用户获准访问此应用程序。

在本教程中，将：

> [!div class="checklist"]
> * 在 MFA 设置之前测试登录体验。
> * 创建一个条件访问策略，它要求用户通过 MFA 才可访问你环境中的云应用。 在本教程中，我们将使用 Microsoft Azure 管理应用来展示此过程。
> * 使用 What If 工具来模拟 MFA 登录情形。
> * 测试条件访问策略。
> * 清理测试用户和策略。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

若要完成本教程中的方案，需要：

 - 对 Access to Azure AD Premium 版本的访问权限，它包含条件访问策略功能。 需创建 Azure AD 条件访问策略，然后才可强制实施 MFA。 请注意，始终在你的组织强制实施 MFA 策略，无论合作伙伴是否具有 MFA 功能。 如果为你的组织设置 MFA，你将需要确保具有足够的 Azure AD Premium 许可证供来宾用户使用。 
 - 有效的外部电子邮件帐户，该帐户可作为来宾用户添加到租户目录中并可在登录时使用。 如果不知道如何创建来宾帐户，请参阅[在 Azure 门户中添加 B2B 来宾用户](add-users-administrator.md)。

## <a name="create-a-test-guest-user-in-azure-ad"></a>在 Azure AD 中创建测试来宾用户

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com/)。
2. 在左窗格中选择“Azure Active Directory”。
3.  在“管理”下，选择“用户”。
4.  选择“新来宾用户”。

    ![选择“Azure Active Directory”](media/tutorial-mfa/tutorial-mfa-user-3.png)

5.  在“用户名”下，输入外部用户的电子邮件地址。 （可选）包含一条欢迎消息。 

    ![选择“Azure Active Directory”](media/tutorial-mfa/tutorial-mfa-user-4.png)

6.  选择“邀请”，以自动向来宾用户发送邀请。 随即显示“已成功邀请用户”消息。 
7.  发送邀请后，该用户帐户将以来宾的形式自动添加到目录。

## <a name="test-the-sign-in-experience-before-mfa-setup"></a>在 MFA 设置之前测试登录体验
1.  使用测试用户名称和密码登录到 [Azure 门户](https://portal.azure.com/)。
2.  请注意，只需使用登录凭据即可访问 Azure 门户。 无需进行其他身份验证。
3.  注销。

## <a name="create-a-conditional-access-policy-that-requires-mfa"></a>创建需要 MFA 的条件访问策略
1.  以全局管理员、安全管理员或条件访问管理员的身份登录到 [Azure 门户](https://portal.azure.com/)。
2.  在 Azure 门户中，选择“Azure Active Directory”。 
3.  在 Azure Active Directory 页面的“安全性”部分中，单击“条件访问”。
4.  在“条件访问”页面顶部的工具栏中，选择“新建策略”。
5.  在“新建”页面的“名称”文本框中，键入“需要 MFA 才能访问 B2B 门户”。
6.  在“分配”部分中，选择“用户和组”。
7.  在“用户和组”页面上，选中“选择用户和组”，然后选择“所有来宾用户(预览版)”。

    ![选择“Azure Active Directory”](media/tutorial-mfa/tutorial-mfa-policy-6.png)
9.  选择“完成”。
10. 在“新建”页面的“分配”部分中，选择“云应用”。
11. 在“云应用”页面上，选中“选择应用”，然后勾选“选择”。

    ![选择“Azure Active Directory”](media/tutorial-mfa/tutorial-mfa-policy-10.png)

12. 在“选择”页面上，选择“Microsoft Azure 管理”，然后勾选“选择”。

    ![选择“Azure Active Directory”](media/tutorial-mfa/tutorial-mfa-policy-11.png)

13. 在“云应用”页面上，选择“完成”。
14. 在“新建”页面的“访问控制”部分中，选择“授权”。
15. 在“授权”页面上，选择“授予访问权限”，然后选择“需要多重身份验证”复选框和“选择”。

    ![选择“Azure Active Directory”](media/tutorial-mfa/tutorial-mfa-policy-13.png)

16. 在“启用策略”下，选择“开”。

    ![选择“Azure Active Directory”](media/tutorial-mfa/tutorial-mfa-policy-14.png)

17. 选择“创建”。

## <a name="use-the-what-if-option-to-simulate-sign-in"></a>使用 What If 选项来模拟登录情形

1.  在“条件访问 - 策略”页面上，选择 What If。 

    ![选择“Azure Active Directory”](media/tutorial-mfa/tutorial-mfa-whatif-1.png)

2.  依次选择“用户”和测试来宾用户，然后选中“选择”。

    ![选择“Azure Active Directory”](media/tutorial-mfa/tutorial-mfa-whatif-2.png)

3.  选择“云应用”。
4.  在“云应用”页面上，选中“选择应用”，然后单击“选择”。 在“选择”页面上，选择“Microsoft Azure 管理”，然后单击“选择”。 

    ![选择“Azure Active Directory”](media/tutorial-mfa/tutorial-mfa-whatif-3.png)

5.  在“云应用”页面上，选择“完成”。
6.  选择 What If，然后验证确保“要应用的策略”选项卡的“评估结果”下显示了新策略。

    ![选择“Azure Active Directory”](media/tutorial-mfa/tutorial-mfa-whatif-4.png)

## <a name="test-your-conditional-access-policy"></a>测试条件访问策略
1.  使用测试用户名称和密码登录到 [Azure 门户](https://portal.azure.com/)。
2.  应会看到有关其他身份验证方法的请求。 请注意，此策略可能一段时间后才会生效。

    ![选择“Azure Active Directory”](media/tutorial-mfa/mfa-required.png)
 
3.  注销。

## <a name="clean-up-resources"></a>清理资源
不再需要测试用户和测试条件访问策略时，请将其删除。
1.  以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com/)。
2.  在左窗格中选择“Azure Active Directory”。
3.  在“管理”下，选择“用户”。
4.  选择测试用户，然后选择“删除用户”。
5.  在左窗格中选择“Azure Active Directory”。
6.  在“安全性”下，选择“条件访问”。
7.  在“策略名称”列表中，为测试策略选择上下文菜单 (…)，然后选择“删除”。 请选择“是”进行确认。
## <a name="next-steps"></a>后续步骤
在本教程中，你创建了一个条件访问策略，它要求来宾用户在登录你的某个云应用时使用 MFA。 要详细了解如何添加来宾用户进行协作，请参阅[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](add-users-administrator.md)。

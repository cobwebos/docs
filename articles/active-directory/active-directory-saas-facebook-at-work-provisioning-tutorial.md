---
title: "教程：使用 Azure Active Directory 为 Workplace by Facebook 配置自动用户预配 | Microsoft Docs"
description: "了解如何从 Azure AD 自动将用户帐户预配到 Workplace by Facebook 以及取消其预配。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 70686a48585d83ca5de78fdded99ae46e90cc20c
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>教程：为 Workplace by Facebook 配置自动用户预配

本教程展示了从 Azure Active Directory (Azure AD) 到 Workplace by Facebook 对用户账户进行自动预配和取消预配所需的步骤。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Workplace by Facebook 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Workplace by Facebook 单一登录 (SSO) 的订阅

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以获取[一个月试用服务](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="assign-users-to-workplace-by-facebook"></a>将用户分配到 Workplace by Facebook

Azure AD 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和组。

在配置和启用预配服务前，请确定 Azure AD 中哪些用户和组表示需要访问 Workplace by Facebook 应用的用户。 然后，可按照[向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的说明将这些用户分配到 Workplace by Facebook 应用。

>[!IMPORTANT]
>*   通过将一个 Azure AD 用户分配到 Workplace by Facebook 来测试预配配置。 稍后分配其他用户和组。
>*   将用户分配到 Workplace by Facebook 时，必须选择有效的用户角色。 默认访问权限角色不可用于预配。

## <a name="enable-automated-user-provisioning"></a>启用自动化用户预配

本部分将指导你完成将 Azure AD 连接到 Workplace by Facebook 的用户账户预配 API。 你还可以了解如何配置预配服务来创建、更新和禁用 Workplace by Facebook 中分配的用户帐户。 这取决于 Azure AD 中的用户和组分配。

>[!Tip]
>还可以选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 Workplace by Facebook 启用基于 SAML 的 SSO。 可以独立于自动预配配置 SSO，尽管这两个功能互相补充。

### <a name="configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>在 Azure AD 中对 Workplace by Facebook 配置用户帐户预配

Azure AD 支持将已分配的用户的帐户详细信息自动同步到 Workplace by Facebook。 此自动同步使 Workplace by Facebook 能够在用户首次尝试登录之前获取对用户进行访问授权所需的数据。 在 Azure AD 中撤销访问权限后，它还可以从 Workplace by Facebook 取消预配用户。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“Azure Active Directory” > “企业应用” > “所有应用程序”。

2. 如果已为 Workplace by Facebook 配置 SSO，请使用搜索字段搜索 Workplace by Facebook 实例。 否则，请选择“添加”并在应用程序库中搜索“Workplace by Facebook”。 从搜索结果中选择“Workplace by Facebook”，并将其添加到应用程序列表。

3. 选择 Workplace by Facebook 实例，并选择“预配”选项卡。

4. 将“预配模式”设置为“自动”。 

    ![Workplace by Facebook 预配选项的屏幕截图](./media/active-directory-saas-facebook-at-work-provisioning-tutorial/provisioning.png)

5. 在“管理凭据”部分下，输入 Workplace by Facebook 管理员的“机密令牌”和“租户 URL”。

6. 在 Azure 门户中，选择“测试连接”，确保 Azure AD 可以连接到 Workplace by Facebook 应用。 如果连接失败，请确保 Workplace by Facebook 帐户具有团队管理员权限。

7. 在“通知电子邮件”字段中输入应接收预配错误通知的人员或组的电子邮件地址，并选中复选框。

8. 选择“保存”。

9. 在“映射”部分，选择“将 Azure Active Directory 用户同步到 Workplace by Facebook”。

10. 在“属性映射”部分中，查看从 Azure AD 同步到 Workplace by Facebook 的用户属性。 选为“匹配”属性的属性将用于匹配 Workplace by Facebook 中的用户帐户以执行更新操作。 若要提交任何更改，请选择“保存”。

11. 若要为 Workplace by Facebook 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”。

12. 选择“保存”。

有关如何配置自动预配的详细信息，请参阅 [Facebook 文档](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)。

现可创建测试帐户。 等待最多 20 分钟即可验证该帐户是否已同步到 Workplace by Facebook。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)
* [配置单一登录](active-directory-saas-facebook-at-work-tutorial.md)


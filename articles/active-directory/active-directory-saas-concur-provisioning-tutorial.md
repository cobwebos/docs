---
title: "教程：Azure Active Directory 与 Concur 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Concur 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: f39013785f000c7055aaa28f4c6f41998644cb6a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-configuring-concur-for-user-provisioning"></a>教程：配置 Concur 以便进行用户预配

本教程旨在介绍为了从 Azure AD 自动将用户帐户预配到 Concur 以及取消其预配而需要在 Concur 和 Azure AD 中执行的步骤。

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定已有以下各项：

*   Azure Active Directory 租户。
*   已启用 Concur 单一登录的订阅。
*   具有团队管理员权限的 Concur 用户帐户。

## <a name="assigning-users-to-concur"></a>将用户分配到 Concur

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中的应用程序的用户和组。

在配置和启用预配服务前，需确定 Azure AD 中的哪些用户和/或组表示需要访问 Concur 应用的用户。 确定后，可按照此处的说明将这些用户分配到 Concur 应用：

[向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>将用户分配到 Concur 的重要提示

*   建议将单个 Azure AD 用户分配到 Concur 来测试预配配置。 其他用户和/或组可以稍后分配。

*   将用户分配到 Concur 时，必须选择有效的用户角色。 “默认访问权限”角色不可用于预配。

## <a name="enable-user-provisioning"></a>启用用户预配

本部分将指导用户完成以下操作：将 Azure AD 连接到 Concur 的用户帐户预配 API，配置预配服务以便基于 Azure AD 中的用户和组分配在 Concur 中创建、更新和禁用所分配的用户帐户。

> [!Tip] 
> 还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 Concur 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-user-account-provisioning"></a>配置用户帐户预配：

本部分的目的是概述如何对 Concur 启用 Active Directory 用户帐户的预配。

若要在费用服务中启用应用，必须正确设置和使用 Web 服务管理配置文件。 不要将 WS 管理员角色添加到用于 T&E 管理功能的现有管理员配置文件。

Concur 顾问或客户管理员必须创建单独的 Web 服务管理员配置文件，并且客户管理员必须将此配置文件用于 Web 服务管理员功能（例如，启用应用）。 这些配置文件必须独立于客户管理员的日常 T&E 管理配置文件（T&E 管理配置文件不应当分配 WSAdmin 角色）。

在创建用于启用应用的配置文件时，需要在用户配置文件字段中输入客户管理员的名称。 这将分配对配置文件的所有权。 创建一个或多个配置文件后，客户必须使用此配置文件登录以在“Web 服务”菜单内针对合作伙伴应用单击“启用”按钮。

出于以下原因，不应当使用用于普通 T&E 管理的配置文件执行此操作。

* 必须由客户在启用应用后显示的对话窗口上单击“是”。 此单击将确认客户同意合作伙伴应用程序访问其数据，因此你或合作伙伴无法单击该“是”按钮。

* 如果使用 T&E 管理员配置文件启用了应用的客户管理员离开了公司（导致该配置文件停用），则使用该配置文件启用的所有应用都无法正常工作，直到使用另一个活动的 WS 管理员配置文件启用该应用。 这就是建议创建单独的 WS 管理配置文件的原因。

* 如果管理员离开了公司，可根据需要将与该 WS 管理员配置文件关联的名称更改为替代管理员，这不会影响已启用的应用，因为无需停用该配置文件。

**若要配置用户预配，请执行以下步骤：**

1. 登录到“Concur”租户。

2. 从“管理”菜单中，选择“Web 服务”。
   
    ![Concur 租户](./media/active-directory-saas-concur-provisioning-tutorial/IC721729.png "Concur tenant")

3. 在左侧，从“Web 服务”窗格中，选择“启用合作伙伴应用程序”。
   
    ![启用合作伙伴应用程序](./media/active-directory-saas-concur-provisioning-tutorial/ic721730.png "Enable Partner Application")

4. 从“启用应用程序”列表中，选择“Azure Active Directory”，并单击“启用”。
   
    ![Microsoft Azure Active Directory](./media/active-directory-saas-concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. 单击“是”以关闭“确认操作”对话框。
   
    ![确认操作](./media/active-directory-saas-concur-provisioning-tutorial/ic721732.png "Confirm Action")

6. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

7. 如果已为 Concur 配置单一登录，请使用搜索字段搜索 Concur 实例。 否则，请选择“添加”并在应用程序库中搜索“Concur”。 从搜索结果中选择 Concur，并将其添加到应用程序列表。

8. 选择 Concur 实例，然后选择“预配”选项卡。

9. 将“预配模式”设置为“自动”。 
 
    ![预配](./media/active-directory-saas-concur-provisioning-tutorial/provisioning.png)

10. 在“管理员凭据”部分，输入 Concur 管理员的“用户名”和“密码”。

11. 在 Azure 门户中，单击“测试连接”，确保 Azure AD 可以连接到 Concur 应用。 如果连接失败，请确保 Concur 帐户具有团队管理员权限。

12. 在“通知电子邮件”字段中输入应接收预配错误通知的人员或组的电子邮件地址，并选中复选框。

13. 单击“保存”。

14. 在“映射”部分，选择“将 Azure Active Directory 用户同步到 Concur”。

15. 在“属性映射”部分，查看从 Azure AD 同步到 Concur 的用户属性。 选为“匹配”属性的特性用于匹配 Concur 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

16. 若要为 Concur 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”

17. 单击“保存”。

现在可创建测试帐户。 等待最多 20 分钟，验证该帐户是否已同步到 Concur。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)
* [配置单一登录](active-directory-saas-concur-tutorial.md)


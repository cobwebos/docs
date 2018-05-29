---
title: 教程：Azure Active Directory 与 Arc Publishing - SSO 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Arc Publishing - SSO 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ae609583-f875-4cb8-b68e-1b0b7938e9a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: 64e02ed953059303250138b655526d75afd3e2b3
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33932545"
---
# <a name="tutorial-azure-active-directory-integration-with-arc-publishing---sso"></a>教程：Azure Active Directory 与 Arc Publishing - SSO 的集成

本教程介绍如何将 Arc Publishing - SSO 与 Azure Active Directory (Azure AD) 集成。

将 Arc Publishing - SSO 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Arc Publishing - SSO。
- 可以让用户使用其 Azure AD 帐户自动登录到 Arc Publishing - SSO（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Arc Publishing - SSO 的集成，需要以下项目：

- Azure AD 订阅
- 已启用 Arc Publishing - SSO 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Arc Publishing - SSO
2. 配置和测试 Azure AD 单一登录

## <a name="adding-arc-publishing---sso-from-the-gallery"></a>从库中添加 Arc Publishing - SSO
若要配置 Arc Publishing - SSO 与 Azure AD 的集成，需要从库中将 Arc Publishing - SSO 添加到托管 SaaS 应用列表。

**若要从库中添加 Arc Publishing - SSO，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入 Arc Publishing - SSO，在结果面板中选择“Arc Publishing - SSO”，单击“添加”按钮添加该应用程序。

    ![结果列表中的 Arc Publishing - SSO](./media/active-directory-saas-arc-tutorial/tutorial_arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分需根据名为“Britta Simon”的测试用户的情况，配置和测试 Arc Publishing - SSO 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Arc Publishing - SSO 用户。 换句话说，需要建立 Azure AD 用户与 Arc Publishing - SSO 中相关用户之间的链接关系。

若要通过 Arc Publishing - SSO 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Arc Publishing - SSO 测试用户](#create-an-arc-publishing---sso-test-user)** - 在 Arc Publishing - SSO 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分的内容是在 Azure 门户中启用 Azure AD 单一登录并在 Arc Publishing - SSO 应用程序中配置单一登录。

**若要配置 Arc Publishing - SSO 的 Azure AD 单一登录，请执行以下步骤**：

1. 在 Azure 门户中的“Arc Publishing - SSO”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-arc-tutorial/tutorial_arc_samlbase.png)

3. 在“Arc Publishing - SSO 域和 URL”部分中，如果要在 IDP 发起的模式下配置应用程序，请执行以下步骤：

    ![Arc Publishing - SSO 域和 URL 单一登录信息](./media/active-directory-saas-arc-tutorial/tutorial_arc_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://www.okta.com/saml2/service-provider/<Unique ID>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

4. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![Arc Publishing - SSO 域和 URL 单一登录信息](./media/active-directory-saas-arc-tutorial/tutorial_arc_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`
     
    > [!NOTE] 
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Arc Publishing - SSO 客户端支持团队](mailto:inf@washpost.com)获取这些值。 

5. Arc Publishing - SSO 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。
    
    ![配置单一登录](./media/active-directory-saas-arc-tutorial/tutorial_arc_attribute.png)

6. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    | 属性名称 | 属性值 |
    | ---------------| --------------- |    
    | firstName | user.givenname |
    | lastName | user.surname |
    | 电子邮件 | user.mail |
    | 组 | user.assignedroles |

    a. 单击“添加属性”，打开“添加属性”对话框。

     ![配置单一登录](./media/active-directory-saas-arc-tutorial/tutorial_attribute_04.png)

     ![配置单一登录](./media/active-directory-saas-arc-tutorial/tutorial_attribute_05.png)
    
    b. 在“名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“值”列表中，选择为该行显示的属性值。

    d. 将“命名空间”留空。
    
    d. 单击“确定”

    > [!NOTE]
    > 此处使用 user.assignedroles 映射组属性。 这些是在 Azure AD 中创建的自定义角色，以将组名称映射回应用程序。 可在[此处](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-enterprise-app-role-management)找到有关如何在 Azure AD 中创建自定义角色的更多指导。 

7. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/active-directory-saas-arc-tutorial/tutorial_arc_certificate.png) 

8. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-arc-tutorial/tutorial_general_400.png)
    
9. 在“Arc Publishing - SSO 配置”部分，单击“配置 Arc Publishing - SSO”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![Arc Publishing - SSO 配置](./media/active-directory-saas-arc-tutorial/tutorial_arc_configure.png) 

10. 若要在 Arc Publishing - SSO 端配置单一登录，需要将下载的证书 (Base64)、注销 URL、SAML 实体 ID 和 SAML 单一登录服务 URL 发送给 [Arc Publishing - SSO 支持团队](mailto:inf@washpost.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-arc-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-arc-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-arc-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-arc-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-an-arc-publishing---sso-test-user"></a>创建 Arc Publishing - SSO 测试用户

本部分要在 Arc Publishing - SSO 中创建名为“Britta Simon”的用户。 Arc Publishing - SSO 支持在默认情况下启用的实时预配。 此部分不存在任何操作项。 如果用户尚不存在，则在尝试访问 Arc Publishing - SSO 期间会创建一个新用户。

>[!Note]
>如果需要手动创建用户，请联系 [Arc Publishing - SSO 支持团队](mailto:inf@washpost.com)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分通过授予 Britta Simon 访问 Arc Publishing - SSO 的权限，使其能够使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Arc Publishing - SSO，请执行以下步骤**：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Arc Publishing - SSO”。

    ![应用程序列表中的 Arc Publishing - SSO 链接](./media/active-directory-saas-arc-tutorial/tutorial_arc_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Arc Publishing - SSO 磁贴时，应会自动登录到 Arc Publishing - SSO 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-arc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-arc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-arc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-arc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-arc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-arc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-arc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-arc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-arc-tutorial/tutorial_general_203.png


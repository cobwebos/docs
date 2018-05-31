---
title: 教程：Azure Active Directory 与 Shmoop For Schools 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Shmoop For Schools 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 799a88344d6c348061af19bfbbd9022025d2d66b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350604"
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>教程：Azure Active Directory 与 Shmoop For Schools 的集成

在本教程中，了解如何将 Shmoop For Schools 与 Azure Active Directory (Azure AD) 集成。

将 Shmoop For Schools 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权限访问 Shmoop For Schools
- 可以让用户使用其 Azure AD 帐户自动登录到 Shmoop For Schools。
- 可在一个中心位置（即 Azure 门户）管理帐户。

有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Shmoop For Schools 的集成，需备齐以下项目：

- Azure AD 订阅
- 已启用 Shmoop For Schools 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

若要测试本教程中的步骤，我们建议：

- 仅在必要时才使用生产环境。
- 如果没有 Azure AD 试用环境，可以获取[一个月试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，会在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Shmoop For Schools
2. 配置和测试 Azure AD 单一登录

## <a name="add-shmoop-for-schools-from-the-gallery"></a>从库中添加 Shmoop For Schools
要配置 Shmoop For Schools 与 Azure AD 的集成，需要从库中将 Shmoop For Schools 添加到托管 SaaS 应用列表。

**若要从库中添加 Shmoop For Schools，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 转到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请选择对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入 **Shmoop For Schools**。 在结果中选择“Shmoop For Schools”，然后选择“添加”按钮添加该应用程序。

    ![结果列表中的 Shmoop For Schools](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 Shmoop For Schools 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Shmoop For Schools 用户。 换句话说，需要在 Azure AD 用户与 Shmoop For Schools 中的相关用户之间建立链接。

若要配置和测试 Shmoop For Schools 的 Azure AD 单一登录，请完成以下构建基块：

1. [配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)，使用户能够使用此功能。
2. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Shmoop For Schools 测试用户](#create-a-shmoop-for-schools-test-user) - 在 Shmoop For Schools 中创建 Britta Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
4. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
5. [测试单一登录](#test-single-sign-on)，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Shmoop For Schools 应用程序中配置单一登录。

**若要配置 Shmoop For Schools 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 Shmoop For Schools 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中“单一登录模式”下面的下拉菜单中，选择“基于 SAML 的登录”。
 
    ![“单一登录”对话框](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. 在“Shmoop For Schools 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. 在“登录 URL”框中，使用以下模式键入 URL：`https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. 在“标识符”框中，使用以下模式键入 URL：`https://schools.shmoop.com/<uniqueid>`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Shmoop For Schools 客户端支持团队](mailto:support@shmoop.com)获取这些值。 
 
4. Shmoop For Schools 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示如何配置断言：

    ![配置单一登录](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Shmoop For Schools 支持两个用户角色：“教师”和“学生”。 在 Azure AD 中设置这些角色，以便可为用户分配相应的角色。 若要了解如何在 Azure AD 中配置角色，请参阅[使用 Azure AD 在云应用程序中进行基于角色的访问控制](http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/)。
    
5. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性。  然后执行以下步骤：

    | 属性名称 | 属性值 |
    | -------------- | --------------- |
    | role           | user.assignedroles |

    a. 若要打开“添加属性”对话框，请选择“添加属性”。
    
    ![配置单一登录 ](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![配置单一登录](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    b. 在“名称”框中，键入为该行显示的属性名称。
    
    c. 在“值”列表中，选择为该行显示的属性值。

    d. 将“命名空间”框留空。
    
    e. 选择“确定”。

6. 选择“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_400.png)

7. 在“SAML 签名证书”部分上，单击”复制”按钮来复制**应用联合元数据 URL**，并将其粘贴到记事本。

    ![证书下载链接](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_certificate.png)

8. 若要在“Shmoop For Schools”端配置单一登录，需要将“应用联合元数据 URL”发送给 [Shmoop For Schools 支持团队](mailto:support@shmoop.com)。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”。 然后选择“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，请在“所有用户”对话框顶部选择“添加”。

    ![“添加”按钮](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择**创建**。
 
### <a name="create-a-shmoop-for-schools-test-user"></a>创建 Shmoop For Schools 测试用户

本部分的目的是在 Shmoop For Schools 中创建名为“Britta Simon”的用户。 Shmoop For Schools 支持默认启用的实时预配。 此部分不存在任何操作项。 如果新用户尚不存在，则尝试访问 Shmoop For Schools 期间，会创建该用户。

>[!NOTE]
>如果需要手动创建用户，请联系 [Shmoop For Schools 支持团队](mailto:support@shmoop.com)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Shmoop For Schools 的权限，以支持其使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Shmoop For Schools，请执行以下步骤：**

1. 在 Azure 门户中，打开应用程序视图。 然后在目录视图中转到“企业应用程序”。  接下来，选择“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Shmoop For Schools”。

    ![应用程序列表中的“Shmoop For Schools”链接](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接][202]

4. 选择“添加”按钮。 然后，在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框中，选择“用户”列表内的“Britta Simon”。

6. 在“用户和组”对话框中，单击“选择”按钮。 

7. 在“添加分配”对话框中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

选择访问面板中的“Shmoop For Schools”磁贴时，应会自动登录到 Shmoop For Schools 应用程序。

有关访问面板的详细信息，请参阅[访问面板简介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_203.png


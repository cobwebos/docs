---
title: 教程：Azure Active Directory 与 Salesforce 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Salesforce 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: jeedes
ms.openlocfilehash: 36f1bd9c11c8932968a3501ef22fdb7153411256
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867555"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>教程：Azure Active Directory 与 Salesforce 集成

在本教程中，了解如何将 Salesforce 与 Azure Active Directory (Azure AD) 集成。

将 Salesforce 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Salesforce。
- 可让用户使用其 Azure AD 帐户自动登录到 Salesforce（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Salesforce 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Salesforce 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Salesforce
2. 配置和测试 Azure AD 单一登录

## <a name="adding-salesforce-from-the-gallery"></a>从库中添加 Salesforce

若要配置 Salesforce 与 Azure AD 的集成，需要从库中将 Salesforce 添加到托管的 SaaS 应用列表。

若要从库中添加 Salesforce，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入 Salesforce，在结果面板中选择“Salesforce”，并单击“添加”按钮来添加该应用程序。

    ![结果列表中的 Salesforce](./media/salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于名为“Britta Simon”的测试用户配置和测试 Salesforce 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Salesforce 用户。 换句话说，需要建立 Azure AD 用户与 Salesforce 中相关用户之间的关联。

可通过将 Azure AD 中“用户名”的值指定为 Salesforce 中“用户名”的值来建立此关联。

若要配置和测试 Salesforce 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Salesforce 测试用户](#create-a-salesforce-test-user) - 在 Salesforce 中创建 Britta Simon 的对应用户，该用户与 Azure AD 中表示 Britta Simon 的用户相关联。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Salesforce 应用程序中配置单一登录。

若要配置 Salesforce 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的“Salesforce” 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 单击屏幕顶部的“更改单一登录模式”，以选择 **SAML** 模式。

    ![配置单一登录链接](./media/salesforce-tutorial/tutorial_general_300.png)

3. 在“选择单一登录方法”对话框中，对 SAML 模式单击“选择”以启用单一登录。

    ![配置单一登录链接](./media/salesforce-tutorial/tutorial_general_301.png)

4. 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“基本 SAML 配置”对话框。
   
    ![配置单一登录链接](./media/salesforce-tutorial/tutorial_general_302.png)

5. 在“基本 SAML 配置”部分中，执行以下步骤：

    ![Salesforce 域和 URL 单一登录信息](./media/salesforce-tutorial/tutorial_salesforce_url.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入值：

    企业帐户：`https://<subdomain>.my.salesforce.com`

    开发人员帐户：`https://<subdomain>-dev-ed.my.salesforce.com`

    b. 在“标识符”文本框中，使用以下模式键入值：

    企业帐户：`https://<subdomain>.my.salesforce.com`

    开发人员帐户：`https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Salesforce 客户端支持团队](https://help.salesforce.com/support)获取这些值。

6. 在“SAML 签名证书”部分中，单击“下载”以下载**联合元数据 XML**，然后将 xml 文件保存在计算机上。

    ![证书下载链接](./media/salesforce-tutorial/tutorial_salesforce_certificate.png) 

7. 在浏览器中打开新选项卡并登录到 Salesforce 管理员帐户。

8. 单击页面右上角设置图标下的“安装”。

    ![配置单一登录](./media/salesforce-tutorial/configure1.png)

9. 向下滚动到导航窗格中的“设置”，单击“标识”，以展开相关部分。 然后单击“单一登录设置”。

    ![配置单一登录](./media/salesforce-tutorial/sf-admin-sso.png)

10. 在“单一登录设置”页上，单击“编辑”按钮。

    ![配置单一登录](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > 如果无法为 Salesforce 帐户启用单一登录设置，则需要联系 [Salesforce 客户端支持团队](https://help.salesforce.com/support)。

11. 选择“已启用 SAML”，并单击“保存”。

      ![配置单一登录](./media/salesforce-tutorial/sf-enable-saml.png)

12. 若要配置 SAML 单一登录设置，请单击“从元数据文件新建”。

    ![配置单一登录](./media/salesforce-tutorial/sf-admin-sso-new.png)

13. 单击“选择文件”以上传从 Azure 门户下载的元数据 XML 文件，然后单击“创建”。

    ![配置单一登录](./media/salesforce-tutorial/xmlchoose.png)

14. 在“SAML 单一登录设置”页上，字段将自动填充，请单击“保存”。

    ![配置单一登录](./media/salesforce-tutorial/salesforcexml.png)

15. 在 Salesforce 的左侧导航窗格中，单击“公司设置”展开相关部分，然后单击“我的域”。

    ![配置单一登录](./media/salesforce-tutorial/sf-my-domain.png)

16. 向下滚动到“身份验证配置”部分，并单击“编辑”按钮。

    ![配置单一登录](./media/salesforce-tutorial/sf-edit-auth-config.png)

17. 在“身份验证配置”部分中，选中“AzureSSO”作为 SAML SSO 配置的“身份验证服务”，然后单击“保存”。

    ![配置单一登录](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > 如果选择了多个身份验证服务，则当用户向 Salesforce 环境发起单一登录时，系统将提示他们选择登录时要使用的身份验证服务。 如果不希望发生这种情况，应将其他所有身份验证服务保持未选中状态。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，选择“Azure Active Directory”，接着选择“用户”，然后选择“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](./media/salesforce-tutorial/create_aaduser_01.png) 

3. 在“用户属性”中，执行以下步骤。

    ![创建 Azure AD 测试用户](./media/salesforce-tutorial/create_aaduser_02.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="create-a-salesforce-test-user"></a>创建 Salesforce 测试用户

在本部分中，将在 Salesforce 中创建名为 Britta Simon 的用户。 Salesforce 支持在默认情况下保持启用的实时预配。 此部分不存在任何操作项。 尝试访问 Salesforce 时，如果 Salesforce 中没有用户，系统会创建一个新用户。 Salesforce 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](salesforce-provisioning-tutorial.md)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Salesforce 的权限，允许其使用 Azure 单一登录。

![分配用户角色][200]

若要将 Britta Simon 分配到 Salesforce，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“Salesforce”。

    ![应用程序列表中的 Salesforce 链接](./media/salesforce-tutorial/tutorial_salesforce_app.png)

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加用户”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Salesforce 磁贴时，用户应自动登录到 Salesforce 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
* [配置用户预配](salesforce-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-tutorial/tutorial_general_203.png
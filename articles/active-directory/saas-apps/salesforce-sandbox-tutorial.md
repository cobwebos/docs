---
title: 教程：Azure Active Directory 与 Salesforce Sandbox 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Salesforce 沙盒之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.openlocfilehash: 48dcb4a3b1e06ab62905092055ba1b48bd0dddb7
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114490"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>教程：Azure Active Directory 与 Salesforce 沙盒集成

本教程介绍如何将 Salesforce 沙盒与 Azure Active Directory (Azure AD) 集成。

使用沙盒可以在不同的环境中针对多种用途（如开发、测试和培训）创建组织的多个副本，而不会影响 Salesforce 产品组织中的的数据和应用程序。
有关更多详细信息，请参阅[沙盒概述](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5)。

将 Salesforce 沙盒与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Salesforce 沙盒。
- 可让用户使用其 Azure AD 帐户自动登录到 Salesforce 沙盒（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Salesforce 沙盒的集成，需要以下项：

- Azure AD 订阅
- 已启用 Salesforce 沙盒单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Salesforce 沙盒
2. 配置和测试 Azure AD 单一登录

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>从库中添加 Salesforce 沙盒

若要配置 Salesforce 沙盒与 Azure AD 的集成，需要从库中将 Salesforce 沙盒添加到托管的 SaaS 应用列表。

**若要从库中添加 Salesforce 沙盒，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入 Salesforce Sandbox，在结果面板中选择“Salesforce Sandbox”，并单击“添加”按钮来添加该应用程序。

    ![结果列表中的 Salesforce 沙盒](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Salesforce 沙盒的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道与 Azure AD 用户相对应的 Salesforce 沙盒用户。 换句话说，需要建立 Azure AD 用户与 Salesforce 沙盒中相关用户之间的关联。

可通过将 Azure AD 中“用户名”的值指定为 Salesforce 沙盒中“用户名”的值来建立此关联。

若要配置和测试 Salesforce 沙盒的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Salesforce 沙盒测试用户](#create-a-salesforce-sandbox-test-user)** - 在 Salesforce 沙盒中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Salesforce 沙盒应用程序中配置单一登录。

**若要配置 Salesforce 沙盒的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Salesforce 沙盒”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 单击屏幕顶部的“更改单一登录模式”，以选择“SAML”模式。

      ![配置单一登录链接](./media/salesforce-sandbox-tutorial/tutorial_general_300.png)

3. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![配置单一登录链接](./media/salesforce-sandbox-tutorial/tutorial_general_301.png)

4. 在“设置 SAML 单一登录”页上，单击“编辑”按钮，以打开“基本 SAML 配置”对话框。
   
    ![配置单一登录链接](./media/salesforce-sandbox-tutorial/tutorial_general_302.png)

5. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“上传元数据文件”。

    ![上传元数据文件](./media/salesforce-sandbox-tutorial/upload_metadata.png)

    b. 单击“文件夹徽标”来选择元数据文件并单击“上传”。

    ![选择元数据文件](./media/salesforce-sandbox-tutorial/browse_upload_metadata.png)

    > [!NOTE]
    > 将从 Salesforce 沙盒管理门户获取服务提供商元数据文件，本教程稍后会对此进行说明。

    c. 成功上传元数据文件后，“回复 URL”文本框会自动填入“回复 URL”值。

    ![Salesforce 沙盒域和 URL 单一登录信息](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

6. 在“SAML 签名证书”部分中，单击“下载”以下载“联合元数据 XML”，然后将 xml 文件保存在计算机上。

    ![证书下载链接](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

7. 在浏览器中打开新选项卡并登录到 Salesforce 沙盒管理员帐户。

8. 单击页面右上角设置图标下的“安装”。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/configure1.png)

9. 向下滚动到左侧导航窗格中的“设置”，单击“标识”，以展开相关部分。 然后单击“单一登录设置”。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. 在“单一登录设置”页上，单击“编辑”按钮。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/configure3.png)

11. 选择“已启用 SAML”，并单击“保存”。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. 若要配置 SAML 单一登录设置，请单击“从元数据文件新建”。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. 单击“选择文件”以上传从 Azure 门户下载的元数据 XML 文件，然后单击“创建”。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/xmlchoose.png)

14. 在“SAML 单一登录设置”页上，字段将自动填充，请单击“保存”。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/salesforcexml.png)

15. 在“单一登录设置”页上，单击“下载元数据”按钮以下载服务提供商元数据文件。 在 Azure 门户的“基本 SAML 配置”部分中使用此文件，以便如上所述配置必要的 URL。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/configure4.png)

16. 如果要在 **SP** 发起的模式下配置应用程序，请满足以下先决条件：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 应有一个已验证的域。

    b. 需在 Salesforce 沙盒中配置并启用域，本教程稍后会介绍相关步骤。

    c. 在 Azure 门户的“基本 SAML 配置”部分中，单击“设置其他 URL”并执行以下步骤：
  
    ![Salesforce 沙盒域和 URL 单一登录信息](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    在“登录 URL”文本框中，使用以下模式键入值：`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > 启用域后，应从 Salesforce 沙盒门户复制此值。

17. 在“SAML 签名证书”部分中，单击“联合元数据 XML”，然后将 xml 文件保存在计算机上。

    ![证书下载链接](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. 在浏览器中打开新选项卡并登录到 Salesforce 沙盒管理员帐户。

19. 单击页面右上角设置图标下的“安装”。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/configure1.png)

20. 向下滚动到左侧导航窗格中的“设置”，单击“标识”，以展开相关部分。 然后单击“单一登录设置”。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

21. 在“单一登录设置”页上，单击“编辑”按钮。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/configure3.png)

22. 选择“已启用 SAML”，并单击“保存”。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

23. 若要配置 SAML 单一登录设置，请单击“从元数据文件新建”。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

24. 单击“选择文件”以上传元数据 XML 文件，然后单击“创建”。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/xmlchoose.png)

25. 在“SAML 单一登录设置”页上，各字段会自动填充数据，在“名称”文本框中键入配置的名称（例如：*SPSSOWAAD_Test*）并单击“保存”。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

26. 若要在 Salesforce 沙盒中启用域，请执行以下步骤：

    > [!NOTE]
    > 在启用域之前，需在 Salesforce 沙盒中创建相同的域。 有关详细信息，请参阅[定义域名](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)。 创建域后，请确保配置正确。

    * 在 Salesforce 沙盒的左侧导航窗格中，单击“公司设置”展开相关部分，然后单击“我的域”。

         ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

    * 在“身份验证配置”部分，单击“编辑”。

        ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

    * 在“身份验证配置”部分，对于“身份验证服务”，请选择在 Salesforce 沙盒中配置 SSO 期间设置的 SAML 单一登录设置的名称，然后单击“保存”。

        ![配置单一登录](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](./media/salesforce-sandbox-tutorial/create_aaduser_01.png) 

3. 在“用户属性”中，按照以下步骤操作。

    ![创建 Azure AD 测试用户](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="create-a-salesforce-sandbox-test-user"></a>创建 Salesforce 沙盒测试用户

在本部分中，将在 Salesforce 沙盒中创建名为 Britta Simon 的用户。 Salesforce 沙盒支持在默认情况下保持启用的实时预配。 此部分不存在任何操作项。 尝试访问 Salesforce 沙盒时，如果 Salesforce 沙盒中没有用户，系统会创建一个新用户。 Salesforce 沙盒还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](salesforce-sandbox-provisioning-tutorial.md)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Salesforce 沙盒的权限，允许其使用 Azure 单一登录。

![分配用户角色][200]

**若要将 Britta Simon 分配到 Salesforce 沙盒，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Salesforce 沙盒”。

    ![应用程序列表中的 Salesforce 沙盒链接](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加用户”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Salesforce 沙盒”磁贴时，用户应自动登录到 Salesforce 沙盒应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
* [配置用户预配](salesforce-sandbox-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png
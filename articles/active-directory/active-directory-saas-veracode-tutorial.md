---
title: "教程：Azure Active Directory 与 Veracode 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Veracode 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: d49349c5ae08e67d91e30967f3644623211823ce
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>教程：Azure Active Directory 与 Veracode 集成

本教程介绍如何将 Veracode 与 Azure Active Directory (Azure AD) 集成。

将 Veracode 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Veracode。
- 可让用户使用其 Azure AD 帐户自动登录到 Veracode（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Veracode 的集成，需备齐以下项目：

- Azure AD 订阅
- 已启用 Veracode 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Veracode
2. 配置和测试 Azure AD 单一登录

## <a name="add-veracode-from-the-gallery"></a>从库中添加 Veracode
若要配置 Veracode 与 Azure AD 的集成，需要从库中将 Veracode 添加到托管 SaaS 应用列表。

**若要从库中添加 Veracode，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中键入“Veracode”，在结果面板中选择“Veracode”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Veracode](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Veracode 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Veracode 用户。 换句话说，需要建立 Azure AD 用户与 Veracode 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Veracode 中“用户名”的值来建立此链接关系。

若要使用 Veracode 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建一个 Veracode 测试用户](#create-a-veracode-test-user)** -若要链接到用户的 Azure AD 表示的 Veracode 中具有 Britta 人 Simon 的副本。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分介绍如何在 Azure 门户中启用 Azure AD 单一登录并在 Veracode 应用程序中配置单一登录。

**若要使用 Veracode 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在“Veracode”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_samlbase.png)

3. 在“Veracode 域和 URL”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成。 

    ![配置单一登录](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_url.png)

4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_certificate.png) 

5. 本部分旨在概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户对 Veracode 进行身份验证。

    Veracode 应用程序需要特定格式的 SAML 断言，这要求向“SAML 令牌属性”配置添加自定义属性映射。 以下屏幕截图显示一个示例。
    
    ![属性](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_attr.png "属性")

6. 若要添加所需的属性映射，请执行以下步骤：

    | 属性名称 | 属性值 |
    |--- |--- |
    | 名 |User.givenname |
    | 姓 |User.surname |
    | email |User.mail |
    
    a. 对于上表中的每个数据行，单击“添加用户属性”。
    
    ![属性](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addattr.png "属性")
    
    ![属性](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addattr1.png "属性")
    
    b. 在“属性名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“属性值”文本框中，选择为该行显示的属性值。
    
    d.单击“下一步”。 单击“确定” 。

7. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-veracode-tutorial/tutorial_general_400.png)

8. 在“Veracode 配置”部分，单击“配置 Veracode”打开“配置登录”窗口。 从“快速参考”部分复制“SAML 实体 ID”。

    ![Veracode 配置](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_configure.png) 

9. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Veracode 公司站点。

10. 在顶部菜单中，单击“设置”，并单击“管理员”。
   
    ![管理](./media/active-directory-saas-veracode-tutorial/ic802911.png "Administration")

11. 单击“SAML”选项卡。

12. 在“组织 SAML 设置”部分中执行以下步骤：
   
    ![管理](./media/active-directory-saas-veracode-tutorial/ic802912.png "Administration")
   
    a.  将从 Azure 门户复制的“SAML 实体 ID”的值粘贴到“颁发者”文本框中。
    
    b. 要上传从 Azure 门户下载的证书，请单击“选择文件”。
   
    c. 选择“启用自助注册”。

13. 在“自助注册设置”部分，执行以下步骤，并单击“保存”：
   
    ![管理](./media/active-directory-saas-veracode-tutorial/ic802913.png "Administration")
   
    a. 对于“新用户激活”，选择“不需要激活”。
   
    b. 对于“用户数据更新”，选择“首选项 Veracode 用户数据”。
   
    c. 对于“SAML 属性的详细信息”，选择以下选项：
      * “用户角色”
      * “策略管理员”
      * “审阅者”
      * “安全主管”
      * “执行经理”
      * “提交者”
      * “创建者”
      * “所有扫描类型”
      * “团队成员身份”
      * “默认团队”

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-veracode-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-veracode-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-veracode-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-veracode-tutorial/create_aaduser_04.png)

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“姓名”框中，键入“BrittaSimon”。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建” 。
 
### <a name="create-a-veracode-test-user"></a>创建 Veracode 测试用户
为使 Azure AD 用户能够登录到 Veracode，必须将其预配到 Veracode。 就 Veracode 来说，预配是自动化任务。 不存在任何操作项。 如有必要，在第一次尝试单一登录时会自动创建用户。

> [!NOTE]
> 可以使用 Veracode 提供的任何其他 Veracode 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。
> 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Veracode 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Veracode，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Veracode”。

    ![应用程序列表中的 Veracode 链接](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Veracode 磁贴，即可自动登录到 Veracode 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_203.png


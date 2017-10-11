---
title: "教程：Azure Active Directory 与 Druva 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Druva 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: b23e73c47b9a00893e036b67826e4b7ead819a1d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>教程：Azure Active Directory 与 Druva 集成

在本教程中，了解如何将 Druva 与 Azure Active Directory (Azure AD) 集成。

将 Druva 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Druva。
- 可以让用户使用其 Azure AD 帐户自动登录到 Druva（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Druva 的集成，需要以下项：

- Azure AD 订阅
- 启用了 Druva 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Druva
2. 配置和测试 Azure AD 单一登录

## <a name="adding-druva-from-the-gallery"></a>从库中添加 Druva
若要配置 Druva 与 Azure AD 的集成，需要从库中将 Druva 添加到托管 SaaS 应用列表。

若要从库中添加 Druva，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中，键入“Druva”，在结果面板中选择“Druva”，然后单击“添加”按钮添加应用程序。

    ![结果列表中的 Druva](./media/active-directory-saas-druva-tutorial/tutorial_druva_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Druva 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Druva 用户。 换句话说，需要建立 Azure AD 用户与 Druva 中相关用户之间的链接关系。

在 Druva 中，将 Azure AD 中“用户名”的值指定为“用户名”的值来建立此链接关系。

若要配置和测试 Druva 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Druva 测试用户](#create-a-druva-test-user) - 在 Druva 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Druva 应用程序中配置单一登录。

若要配置 Druva 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的“Druva”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-druva-tutorial/tutorial_druva_samlbase.png)

3. 在“Druva 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-druva-tutorial/tutorial_druva_url.png)

    在“登录 URL”文本框中，键入 URL：`https://cloud.druva.com/home`

4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/active-directory-saas-druva-tutorial/tutorial_druva_certificate.png) 

5. Druva 应用程序需要特定格式的 SAML 断言，这要求向“SAML 令牌属性配置”添加自定义属性映射。  

    ![配置单一登录](./media/active-directory-saas-druva-tutorial/tutorial_druva_attribute.png)

6. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，再执行以下步骤：

    | 属性名称      | 属性值      |
    | ------------------- | -------------------- |
    | insync\_auth\_token |输入令牌生成的值 |
    
    a. 单击“添加属性”，打开“添加属性”对话框。
    
    ![配置单一登录](./media/active-directory-saas-druva-tutorial/tutorial_attribute_04.png)
    
    ![配置单一登录](./media/active-directory-saas-druva-tutorial/tutorial_attribute_05.png)
    
    b.保留“数据库类型”设置，即设置为“共享”。 在“名称”文本框中，键入为该行显示的属性名称。

    c. 在“值”列表中，选择为该行显示的属性值。 本教程后面部分将介绍令牌生成的值。
    
    d.单击“下一步”。 单击“确定” 。    

7. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-druva-tutorial/tutorial_general_400.png)

8. 在“Druva 配置”部分，单击“配置 Druva”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL 和 SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-druva-tutorial/tutorial_druva_configure.png) 

9. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Druva 公司站点。

10. 转到“管理”\>“设置”。

    ![设置](./media/active-directory-saas-druva-tutorial/ic795091.png "设置")

11. 在“单一登录设置”对话框上，执行以下步骤：

    ![单一登录设置](./media/active-directory-saas-druva-tutorial/ic795092.png "Single Sign-On Settings")
    
    a. 在“ID 提供者登录 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。
    
    b. 在“ID 提供者注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。
    
     c. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，再粘贴到“ID 提供者证书”文本框中。
     
     d.单击“下一步”。 若要打开“设置”页，请单击“保存”。

12. 在“设置”页上，单击“生成 SSO 令牌”。

    ![设置](./media/active-directory-saas-druva-tutorial/ic795093.png "设置")

13. 在“单一登录身份验证令牌”对话框中，执行以下步骤：

    ![SSO 令牌](./media/active-directory-saas-druva-tutorial/ic795094.png "SSO Token")
    
    a. 单击“复制”，在“添加属性”部分的“值”文本框中粘贴复制的值。
    
    b. 单击“**关闭**”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-druva-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-druva-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-druva-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-druva-tutorial/create_aaduser_04.png)

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“姓名”框中，键入“BrittaSimon”。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建” 。
 
### <a name="create-a-druva-test-user"></a>创建 Druva 测试用户

为了使 Azure AD 用户能够登录到 Druva，必须将其预配到 Druva 中。 对于 Druva，需要手动执行预配。

**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录到 **Druva** 公司站点。

2. 转到“管理”\>“用户”。
   
   ![管理用户](./media/active-directory-saas-druva-tutorial/ic795097.png "管理用户")

3. 单击“新建”。
   
   ![管理用户](./media/active-directory-saas-druva-tutorial/ic795098.png "管理用户")

4. 在“新建用户”对话框中，执行以下步骤：
   
   ![新建用户](./media/active-directory-saas-druva-tutorial/ic795099.png "Create NewUser")
   
   a. 在“电子邮件地址”文本框中，输入用户的电子邮件地址（例如 brittasimon@contoso.com）。
   
   b. 在“名称”文本框中输入用户的姓名，例如“BrittaSimon”。
   
   c. 单击“创建用户”。

>[!NOTE]
>可以使用 Druva 提供的任何其他 Druva 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Druva 的权限，以支持其使用 Azure 单一登录。

![分配用户角色][200] 

若要将 Britta Simon 分配到 Druva，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Druva”。

    ![应用程序列表中的 Druva 链接](./media/active-directory-saas-druva-tutorial/tutorial_druva_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Druva 磁贴时，应该会自动登录 Druva 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-druva-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-druva-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-druva-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-druva-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-druva-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-druva-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-druva-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-druva-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-druva-tutorial/tutorial_general_203.png


---
title: "教程：Azure Active Directory 与 Shmoop For Schools 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Shmoop For Schools 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: 667c8fc840a918635dcd5e6838afbadb50cc1b97
ms.contentlocale: zh-cn
ms.lasthandoff: 09/21/2017

---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>教程：Azure Active Directory 与 Shmoop For Schools 的集成

在本教程中，了解如何将 Shmoop For Schools 与 Azure Active Directory (Azure AD) 集成。

将 Shmoop For Schools 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权限访问 Shmoop For Schools
- 可以让用户使用其 Azure AD 帐户自动登录到 Shmoop For Schools（单一登录）
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Shmoop For Schools 的集成，需备齐以下项目：

- Azure AD 订阅
- 已启用 Shmoop For Schools 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Shmoop For Schools
2. 配置并测试 Azure AD 单一登录

## <a name="adding-shmoop-for-schools-from-the-gallery"></a>从库中添加 Shmoop For Schools
要配置 Shmoop For Schools 与 Azure AD 的集成，需要从库中将 Shmoop For Schools 添加到托管 SaaS 应用列表。

**若要从库中添加 Shmoop For Schools，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中，键入“Shmoop For Schools”，在结果面板中选择“Shmoop For Schools”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Shmoop For Schools](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 Shmoop For Schools 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Shmoop For Schools 用户。 换句话说，需要建立 Azure AD 用户与 Shmoop For Schools 中相关用户之间的关联关系。

将 Azure AD 中“用户名”的值指定为 Shmoop For Schools 中“用户名”的值，从而建立此关联关系。

若要配置和测试 Shmoop For Schools 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Shmoop For Schools 测试用户](#create-a-shmoop-for-schools-test-user)** - 在 Shmoop For Schools 中创建 Britta Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Shmoop For Schools 应用程序中配置单一登录。

**若要配置 Shmoop For Schools 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 Shmoop For Schools 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. 在“Shmoop For Schools 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://schools.shmoop.com/<uniqueid>`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Shmoop For Schools 客户端支持团队](mailto:support@shmoop.com)获取这些值。 
 
4. Shmoop For Schools 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。

    ![配置单一登录](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Shmoop For Schools 支持两个用户角色 -“教师”和“学生”。 需要在 Azure AD 中设置这些角色，以便可为用户分配相应的角色。 请参阅[链接](http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/)了解如何在 Azure AD 中配置角色。
    
5. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    | 属性名称 | 属性值 |
    | -------------- | --------------- |
    | role           | user.assignedroles |

    a. 单击“添加属性”，打开“添加属性”对话框。
    
    ![配置单一登录](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![配置单一登录](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    b.保留“数据库类型”设置，即设置为“共享”。 在“名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“值”列表中，选择为该行显示的属性值。

    d.单击“下一步”。 将“命名空间”文本框保留为空。
    
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 单击“确定” 。

6. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_400.png)

7. 若要生成**元数据** URL，请执行以下步骤：

    a. 单击“应用注册”。
    
    ![配置单一登录](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_appregistrations.png)
   
    b. 单击“终结点”以打开“终结点”对话框。  
    
    ![配置单一登录](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_endpointicon.png)

    c. 单击复制按钮以复制**联合元数据文档** URL 并将其粘贴到记事本。
    
    ![配置单一登录](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_endpoint.png)
     
    d.单击“下一步”。 现在，转到“Shmoop For Schools”的属性页，使用“复制”按钮复制“应用程序 ID”并将其粘贴到记事本。
 
    ![配置单一登录](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_appid.png)

    e. 使用以下模式生成**元数据 URL**：`<FEDERATION METADATA DOCUMENT url>?appid=<application id>`   

8. 若要在“Shmoop For Schools”端配置单一登录，需要将**元数据 URL** 发送给 [Shmoop For Schools 支持团队](mailto:support@shmoop.com)。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_04.png)

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“姓名”框中，键入“BrittaSimon”。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建” 。
 
### <a name="create-a-shmoop-for-schools-test-user"></a>创建 Shmoop For Schools 测试用户

本部分的目的是在 Shmoop For Schools 中创建名为“Britta Simon”的用户。 Shmoop For Schools 支持在默认情况下启用的实时预配。 此部分不存在任何操作项。 尝试访问 Shmoop For Schools 期间，如果尚不存在用户，则会创建一个新用户。

>[!Note]
>如果需要手动创建用户，请联系 [Shmoop For Schools 支持团队](mailto:support@shmoop.com)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Shmoop For Schools 的权限，以支持其使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Shmoop For Schools，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Shmoop For Schools”。

    ![应用程序列表中的“Shmoop For Schools”](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Shmoop For Schools”磁贴时，应会自动登录到 Shmoop For Schools 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

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



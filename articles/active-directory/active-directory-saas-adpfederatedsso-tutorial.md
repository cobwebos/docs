---
title: "教程：Azure Active Directory 与 ADP Federated SSO 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 ADP Federated SSO 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: jeedes
ms.openlocfilehash: ad12dfd525afe1bde7026535dceb25556abf0a96
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adp-federated-sso"></a>教程：Azure Active Directory 与 ADP Federated SSO 的集成

本教程介绍如何将 ADP Federated SSO 与 Azure Active Directory (Azure AD) 集成。

将 ADP Federated SSO 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 ADP Federated SSO
- 可使用户通过其 Azure AD 帐户自动登录 ADP Federated SSO（单一登录）
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 ADP Federated SSO 的集成，需要以下项目：

- Azure AD 订阅
- 启用了 ADP Federated SSO 的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 ADP Federated SSO
2. 配置和测试 Azure AD 单一登录

## <a name="adding-adp-federated-sso-from-the-gallery"></a>从库中添加 ADP Federated SSO
若要配置 ADP Federated SSO 与 Azure AD 的集成，需要从库中将 ADP Federated SSO 添加到托管 SaaS 应用列表。

**若要从库中添加 ADP Federated SSO，请执行以下步骤：**

1.  以管理员身份登录到 Microsoft Azure 标识提供者环境。

2. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

3. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
4. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

5. 在搜索框中键入 **ADP Federated SSO**，在结果面板中选择“ADP Federated SSO”，单击“添加”按钮添加该应用程序。

    ![结果列表中的 ADP Federated SSO](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分需根据名为“Britta Simon”的测试用户的情况，配置和测试 ADP Federated SSO 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 ADP Federated SSO 用户。 换句话说，需要建立 Azure AD 用户与 ADP Federated SSO 中相关用户之间的链接关系。

在 ADP Federated SSO 中，将 Azure AD 中“用户名”的值指定为“用户名”的值来建立此链接关系。

若要通过 ADP Federated SSO 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 ADP Federated SSO 测试用户](#create-an-adp-federated-sso-test-user)** - 在 ADP Federated SSO 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 ADP Federated SSO 应用程序中配置单一登录。

**若要配置 ADP Federated SSO 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 **ADP Federated SSO** 应用程序集成页上，单击“属性”选项卡并执行以下步骤： 

    ![单一登录属性](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_prop.png)

    a. 将“允许用户登录”字段值设置为“是”。

    b. 复制“用户访问 URL”并将其粘贴到“配置登录 URL”部分，教程稍后将对此进行说明。

    c. 将“需要进行用户分配”字段值设置为“是”。

    d.单击“下一步”。 将“对用户可见”字段值设置为“否”。

2. 在 **ADP Federated SSO** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

3. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_samlbase.png)

4. 在“ADP Federated SSO 域和 URL”部分中，执行以下步骤：

    ![ADP Federated SSO 域和 URL 单一登录信息](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_url.png)

    在“标识符”文本框中，键入一个 URL：`https://fed.adp.com/` 
    
5. ADP Federated SSO 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示一个示例。 声明名称始终是“PersonImmutableID”，其值已映射到 **employeeid**。 

    此处，将用户从 Azure AD 映射到 ADP Federated SSO 是在 **employeeid** 上完成的，但可将其映射到基于应用程序设置的其他值。 因此，首先请与 [ADP 支持团队](https://www.adp.com/contact-us/overview.aspx)协作，使用用户的正确标识符，并将该值与“PersonImmutableID”声明一起映射。

    ![配置单一登录](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_attribute.png)

6. 在“单一登录”对话框的“用户属性”部分，按图中所示配置 SAML 令牌属性，然后执行以下步骤：
    
    | 属性名称 | 属性值 |
    | ------------------- | -------------------- |    
    | PersonImmutableID | user.employeeid |
    
    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_04.png)

    ![配置单一登录](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_05.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 在“值”列表中，选择为该行显示的属性值。
    
    d.单击“下一步”。 单击“确定” 。

    > [!NOTE] 
    > 在配置 SAML 断言前，需要联系 [ADP 支持团队](https://www.adp.com/contact-us/overview.aspx)，并为租户请求唯一标识符属性值。 拥有此值才能为应用程序配置自定义声明。 

7. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_certificate.png) 

8. 若要在 **ADP Federated SSO** 端配置单一登录，需在 [ADP Federated SSO 网站](https://adpfedsso.adp.com/public/login/index.fcc)中上传已下载的**元数据 XML**。

> [!NOTE]  
> 此过程可能需要几天时间。 

### <a name="configure-your-adp-services-for-federated-access"></a>配置 ADP 服务进行联合访问

>[!Important]
> 必须将需要联合访问 ADP 服务的员工分配到 ADP 服务应用，然后，必须将用户重新分配到特定的 ADP 服务。
收到 ADP 代表的确认后，请配置 ADP 服务并分配/管理用户，以控制用户对特定 ADP 服务的访问。

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入 **ADP Federated SSO**，在结果面板中选择“ADP Federated SSO”，单击“添加”按钮添加该应用程序。

    ![结果列表中的 ADP Federated SSO](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_addservicegallery.png)

5. 在 Azure 门户中的 **ADP Federated SSO** 应用程序集成页上，单击“属性”选项卡并执行以下步骤：  

    ![单一登录 linkedproperties](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linkedproperties.png)

    a.  将“允许用户登录”字段值设置为“是”。

    b.  将“需要进行用户分配”字段值设置为“是”。

    c.  将“对用户可见”字段值设置为“是”。

6. 在 **ADP Federated SSO** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

7. 在“单一登录”对话框中，将“模式”选择为“链接登录”，将应用程序链接到“ADP 联合 SSO”。

    ![单一登录已链接](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linked.png)

8. 导航到“配置登录 URL”部分，执行以下步骤：

    ![单一登录属性](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linkedsignon.png)
                                                              
    a. 粘贴从上述**属性选项卡**复制的“用户访问 URL”（在 ADP Federated SSO 主应用中）。
                                                             
    b. 以下 5 个不同的应用支持“中继状态 URL”。 必须手动将特定应用程序的相应“中继状态 URL”值追加到“用户访问 URL”。
    
    * **ADP Workforce Now**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **保存**更改。

10. 收到 ADP 代表的确认后，可以开始使用一个或两个用户进行测试。

    a. 将少量的用户分配到 ADP 服务应用，以测试联合访问。

    b. 如果这些用户能够访问库中的 ADP 服务应用，并且能够访问其 ADP 服务，则表示测试成功。
 
11. 确认测试成功后，将联合的 ADP 服务分配到单个用户或用户组（本教程稍后会介绍），并向员工推出该服务。 

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d.单击“下一步”。 单击“创建”。
 
### <a name="create-an-adp-federated-sso-test-user"></a>创建 ADP Federated SSO 测试用户

本部分要在 ADP Federated SSO 中创建名为“Britta Simon”的用户。 请与 [ADP 支持团队](https://www.adp.com/contact-us/overview.aspx)协作，在 ADP Federated SSO 帐户中添加用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分通过授予 Britta Simon 访问 ADP Federated SSO 的权限，使其能够使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 ADP Federated SSO，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在“应用程序列表”中，选择“ADP Federated SSO”。

    ![应用程序列表中的 ADP Federated SSO 链接](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 ADP Federated SSO 磁贴时，应会自动登录到 ADP Federated SSO 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_203.png


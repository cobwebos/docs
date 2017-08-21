---
title: "教程：Azure Active Directory 与 Workday 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Workday 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 164d5c644f120fa86e2b690649241892764b64b7
ms.contentlocale: zh-cn
ms.lasthandoff: 07/11/2017


---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>教程：Azure Active Directory 与 Workday 的集成

本教程介绍如何将 Workday 与 Azure Active Directory (Azure AD) 集成。

将 Workday 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Workday
- 可让用户使用其 Azure AD 帐户自动登录 Workday（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Workday 的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 Workday 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Workday
2. 配置和测试 Azure AD 单一登录

## <a name="adding-workday-from-the-gallery"></a>从库中添加 Workday
若要配置 Workday 与 Azure AD 的集成，需要从库中将 Workday 添加到托管 SaaS 应用列表。

若要从库中添加 Workday，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 **Workday**。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-workday-tutorial/tutorial_workday_search.png)

5. 在结果面板中，选择“Workday”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-workday-tutorial/tutorial_workday_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Workday 的 Azure AD 单一登录。

为了使单一登录能正常工作，Azure AD 需要知道 Workday 中与 Azure AD 用户相对应的用户。 换句话说，需要在 Azure AD 用户与 Workday 中的相关用户间建立关联。

将 Azure AD 中“用户名”的值指定为 Workday 中“用户名”的值来建立此关联。

若要配置和测试 Workday 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Workday 测试用户](#creating-a-workday-test-user) - 在 Workday 中创建 Britta Simon 的对应用户，其与用户的 Azure AD 表示形式相关联。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Workday 应用程序中配置单一登录。

若要配置 Workday 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的 Workday 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-workday-tutorial/tutorial_workday_samlbase.png)

3. 在“Workday 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-workday-tutorial/tutorial_workday_url.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，键入 `https://impl.workday.com/<tenant>/login-saml2.htmld` 值

    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 在“回复 URL”文本框中，使用以下模式键入 URL：`https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE] 
    > 这些不是实际值。 请使用实际的登录 URL 和回复 URL 更新这些值。 回复 URL 必须具有一个子域（例如：www、wd2、wd3、wd3-impl、wd5 和 wd5-impl）。 可以使用类似于“*http://www.myworkday.com*”的 URL，但不能使用“*http://myworkday.com*”的 URL。 请联系 [Workday 客户端支持团队](https://www.workday.com/en-us/partners-services/services/support.html)获取这些值。 
 

4. 在“SAML 签名证书”部分中，单击“证书(Base64)”，然后在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-workday-tutorial/tutorial_workday_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-workday-tutorial/tutorial_general_400.png)

6. 在“Workday 配置”部分，单击“配置 Workday”，打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-workday-tutorial/tutorial_workday_configure.png) 
<CS>
7. 在另一 Web 浏览器窗口中，以管理员身份登录 Workday 公司站点。

8. 转到“菜单”\>“工作台”。
   
    ![工作台](./media/active-directory-saas-workday-tutorial/IC782923.png "工作台")

9. 转到“帐户管理”。
   
    ![帐户管理](./media/active-directory-saas-workday-tutorial/IC782924.png "帐户管理")

10. 转到“编辑租户设置 - 安全”。
   
    ![编辑租户安全性](./media/active-directory-saas-workday-tutorial/IC782925.png "编辑租户安全性")

11. 在“重定向 URL”部分中，执行以下步骤：
   
    ![重定向 URL](./media/active-directory-saas-workday-tutorial/IC7829581.png "重定向 URL")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“添加行”。
   
    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 在“登录重定向 URL”文本框和“移动重定向 URL”文本框中，键入在 Azure 门户的“Workday 域和 URL”部分中输入的登录 URL。
   
    c. 在 Azure 门户的“配置登录”窗口中，复制“注销 URL”，然后将其粘贴到“注销重定向 URL”文本框。
   
    d.  在“环境”文本框中，键入环境名称。  

    >[!NOTE]
    > “环境”属性的值与租户 URL 的值绑定：  
    >如果 Workday 租户 URL 的域名以 impl 开头（例如：https://impl.workday.com/\<tenant\>/login-saml2.htmld），则“环境”属性必须设置为“实现”。  
    >如果域名以其他内容开头，则需要联系 [Workday 客户端支持团队](https://www.workday.com/en-us/partners-services/services/support.html)，获取匹配的“环境”值。

12. 在“SAML 设置”部分中执行以下步骤：
   
    ![SAML 设置](./media/active-directory-saas-workday-tutorial/IC782926.png "SAML 设置")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  选择“启用 SAML 身份验证”。
   
    b.  单击“添加行”。

13. 在“SAML 标识提供者”部分中，执行以下步骤：
   
    ![SAML 标识提供者](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML 标识提供者")
   
    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“标识提供者名称”文本框中，键入提供者名称（例如：SPInitiatedSSO）。
   
    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 在 Azure 门户的“配置登录”窗口中，复制“SAML 实体 ID”值，然后将其粘贴到“颁发者”文本框。
   
    c. 选择“启用 Workday 发起的注销”。
   
    d. 在 Azure 门户的“配置登录”窗口中，复制“注销 URL”值，然后将其粘贴到“注销请求 URL”文本框。

    e. 单击“标识提供者公钥证书”，然后单击“创建”。 

    ![创建](./media/active-directory-saas-workday-tutorial/IC782928.png "创建")

    f. 单击“创建 x509 公钥”。 

    ![创建](./media/active-directory-saas-workday-tutorial/IC782929.png "创建")


14. 在“查看 x509 公钥”部分中，执行以下步骤： 
   
    ![查看 x509 公钥](./media/active-directory-saas-workday-tutorial/IC782930.png "查看 x509 公钥") 
   
    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“名称”文本框中，键入证书名称（例如：PPE\_SP）。
   
    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 在**有效起始日期**文本框中，键入证书的“有效起始日期”属性值。
   
    c.  在**有效截止日期**文本框中，键入证书的“有效截止日期”属性值。
   
    > [!NOTE]
    > 可以双击下载的证书，以从中获取有效起始日期和有效截止日期。  该日期在“详细信息”选项卡下列出。
    > 
    >
   
    d.  在记事本中打开 Base-64 编码的证书，然后复制其内容。
   
    e.  在“证书”文本框中，粘贴剪贴板的内容。
   
    f.  单击 **“确定”**。

15. 执行以下步骤： 
   
    ![SSO 配置](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguratio.png "SSO 配置")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  启用“x509 私钥对”。
   
    b.  在“服务提供商 ID”文本框中，键入 **http://www.workday.com**。
   
    c.  选择“启用 SP 发起的 SAML 身份验证”。
   
    d.  在 Azure 门户的“配置登录”窗口中，复制“SAML 单一登录服务 URL”值，然后将其粘贴到“IdP SSO 服务 URL”文本框。
   
    e. 选择“不削弱 SP 发起的身份验证请求”。
   
    f. 对于**身份验证请求签名方法**，请选择 **SHA256**。 
   
    ![身份验证请求签名方法](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguration.png "身份验证请求签名方法") 
   
    g. 单击 **“确定”**。 
   
    ![确定](./media/active-directory-saas-workday-tutorial/IC782933.png "确定")
<CE>

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
>

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-workday-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-workday-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-workday-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-workday-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-a-workday-test-user"></a>创建 Workday 测试用户

要将测试用户预配到 Workday 中，需要联系 [Workday 客户端支持团队](https://www.workday.com/en-us/partners-services/services/support.html)。
[Workday 客户端支持团队](https://www.workday.com/en-us/partners-services/services/support.html)将创建用户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分中，通过授予 Britta Simon 访问 Workday 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 Workday，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Workday”。

    ![配置单一登录](./media/active-directory-saas-workday-tutorial/tutorial_workday_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)
* [配置用户预配](active-directory-saas-workday-inbound-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-workday-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workday-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workday-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workday-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workday-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workday-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workday-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workday-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workday-tutorial/tutorial_general_203.png



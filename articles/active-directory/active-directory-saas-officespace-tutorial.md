---
title: "教程：Azure Active Directory 与 OfficeSpace Software 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 OfficeSpace Software 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: fb61a2d622b0d829a134b6ce5dfef6e9fb44fa1e
ms.lasthandoff: 03/22/2017


---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>教程：Azure Active Directory 与 OfficeSpace Software 的集成

在本教程中，了解如何将 OfficeSpace Software 与 Azure Active Directory (Azure AD) 集成。

将 OfficeSpace Software 与 Azure AD 集成具有以下优势：

- 可以在 Azure AD 中控制谁有权访问 OfficeSpace Software
- 可以让用户使用其 Azure AD 帐户自动登录到 OfficeSpace Software（单一登录）
- 可在一个中心位置（即 Azure 管理门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 OfficeSpace Software 的集成，需要具有以下项：

- Azure AD 订阅
- 启用 OfficeSpace Software 单一登录的订阅


> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。


测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。


## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 OfficeSpace Software
2. 配置和测试 Azure AD 单一登录


## <a name="adding-officespace-software-from-the-gallery"></a>从库中添加 OfficeSpace Software
若要配置 OfficeSpace Software 与 Azure AD 的集成，需要从库中将 OfficeSpace Software 添加到托管 SaaS 应用列表。

**若要从库中添加 OfficeSpace Software，请执行以下步骤：**

1. 在 **[Azure 管理门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“OfficeSpace Software”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_001.png)

5. 在结果面板中，选择“OfficeSpace Software”，然后单击“添加”按钮以添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 OfficeSpace Software 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 OfficeSpace Software 用户。 换句话说，需要在 Azure AD 用户与 OfficeSpace Software 中的相关用户之间建立关联关系。

可以通过将 Azure AD 中“用户名”的值分配为 OfficeSpace Software 中“用户名”的值来建立此关联关系。

若要配置并测试 OfficeSpace Software 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 OfficeSpace Software 测试用户](#creating-an-officespace-software-test-user)** - 在 OfficeSpace Software 中创建 Britta Simon 的对应用户，将其关联到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 管理门户中启用 Azure AD 单一登录并在 OfficeSpace Software 应用程序中配置单一登录。

**若要配置 OfficeSpace Software 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 管理门户的“OfficeSpace Software”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_01.png)

3. 在“OfficeSpace Software 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_02.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. 在“标识符”文本框中，使用以下模式键入值：`<company name>.officespacesoftware.com`

    > [!NOTE] 
    > 请注意，这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 联系 [OfficeSpace Software 支持团队](mailto:support@officespacesoftware.com)以获取这些值。 

4. OfficeSpace Software 应用程序需要采用特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。
    
    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_03.png)

5. 在“单一登录”对话框上的“用户属性”部分中，选择“user.mail”作为**用户标识符**，并针对下表中所示的每一行，执行以下步骤：
    
    | 属性名称 | 属性值 |
    | --- | --- |    
    | email | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_04.png)

    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_05.png)
    
    b.保留“数据库类型”设置，即设置为“共享”。 在“名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“值”列表中，选择为该行显示的属性值。
    
    d.单击“下一步”。 单击“确定”

6. 在“SAML 签名证书”部分中，单击“证书(base64)”，然后在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_08.png) 

7. 单击“保存” 。

    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_general_400.png)

8. 在“OfficeSpace Software 配置”部分中，单击“配置 OfficeSpace Software”以打开“配置登录”窗口。

    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_09.png) 

    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_10.png)

9. 在另一 Web 浏览器窗口中，以管理员身份登录到 OfficeSpace Software 租户。

10. 转到“设置”，然后单击“连接器”。

    ![在应用端配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_002.png)

11. 单击“SAML 身份验证”。

    ![在应用端配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_003.png)

12. 在“SAML 身份验证”部分中，执行以下步骤：

    ![在应用端配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_004.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“注销提供程序 URL”文本框中，放置 Azure AD 应用程序配置窗口中“注销 URL”的值。

    b. 在“客户端 IDP 目标 URL”文本框中，放置 Azure AD 应用程序配置窗口中“SAML 单一登录服务 URL”的值。

    c. 从下载的证书中复制“指纹”值，然后将其粘贴到“客户端 idp 证书指纹”文本框中。 

    d.单击“下一步”。 单击“保存设置”。

    > [!NOTE]
    > 有关详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI) 
  

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 管理门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-officespace-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-officespace-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-officespace-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-officespace-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。 



### <a name="creating-an-officespace-software-test-user"></a>创建 OfficeSpace Software 测试用户

本部分的目的是在 OfficeSpace Software 中创建名为“Britta Simon”的用户。 OfficeSpace Software 支持在默认情况下启用的实时预配。

此部分不存在任何操作项。 尝试访问 OfficeSpace Software 期间，如果该用户尚不存在，则将创建一个新用户。

> [!NOTE]
> 如果需要手动创建用户，则需联系 [OfficeSpace Software 支持团队](mailto:support@officespacesoftware.com)。


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分需授予 Britta Simon 访问 OfficeSpace Software 的权限，使之能够使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 OfficeSpace Software，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“OfficeSpace Software”。

    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_50.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    


### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 OfficeSpace Software 磁贴时，应当会自动登录到 OfficeSpace Software 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_203.png

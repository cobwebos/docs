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
ms.date: 12/08/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 01e2bcf3fa32546a7952ddc919f99b46a74755a2
ms.openlocfilehash: e0933b1a7e19fc70f9c5e81225b296412837385e


---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>教程：Azure Active Directory 与 OfficeSpace Software 的集成

在本教程中，了解如何将 OfficeSpace Software 与 Azure Active Directory (Azure AD) 集成。

将 OfficeSpace Software 与 Azure AD 集成具有以下优势：

- 可以在 Azure AD 中控制谁有权访问 OfficeSpace Software
- 可以让用户使用其 Azure AD 帐户自动登录到 OfficeSpace Software（单一登录）
- 可在一个中心位置（即 Azure 经典门户）管理帐户

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

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 

    ![Active Directory][1]

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。

    ![应用程序][2]

4. 在页面底部单击“添加”。

    ![应用程序][3]

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。

    ![应用程序][4]

6. 在搜索框中，键入“OfficeSpace Software”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_001.png)

7. 在结果窗格中，选择“OfficeSpace Software”，然后单击“完成”添加该应用程序。

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

本部分的目的是在 Azure 经典门户中启用 Azure AD 单一登录并在 OfficeSpace Software 应用程序中配置单一登录。

OfficeSpace Software 应用程序需要采用特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可从应用程序的“属性”选项卡管理这些属性的值。 以下屏幕截图显示一个示例。 

![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_01.png)

**若要配置 OfficeSpace Software 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“OfficeSpace Software”应用程序集成页的顶部菜单中，单击“属性”。

    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_02.png)

2. 在“SAML 令牌属性”对话框中，对于下表中显示的每个行，执行以下步骤：
    
    | 属性名称 | 属性值 |
    | --- | --- |    
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | user.mail |
    | 电子邮件 | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“添加用户属性”，打开“添加用户属性”对话框。

    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_03.png)
    
    b. 在“属性名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“属性值”列表中，键入为该行显示的属性值。
    
    d.单击“下一步”。 单击“完成”

3. 在顶部菜单中，单击“快速启动”。

    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_04.png) 

4. 在经典门户中的“OfficeSpace Software”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。

    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_05.png)

5. 在“你希望用户如何登录 OfficeSpace Software”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
 
    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_06.png)

6. 在“配置应用设置”对话框页上，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_07.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<company name>.officespace.com/users/sign_in/saml`

    b. 单击“资源组名称” 的 Azure 数据工厂。

    > [!NOTE] 
    > 请注意，这不是实际值。 必须使用实际登录 URL 更新此值。 联系 [OfficeSpace Software 支持团队](emaiLto:support@officespacesoftware.com)以获取此值。

7. 在“配置 OfficeSpace Software 的单一登录”页上，请单击“下载证书”，然后在计算机上保存该文件：

    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_08.png) 

8. 在另一 Web 浏览器窗口中，以管理员身份登录到 OfficeSpace Software 租户。

9. 转到“管理员”，然后单击“连接器”。

    ![在应用端配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_002.png)

10. 单击“SAML 授权”。

    ![在应用端配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_003.png)

11. 在“SAML 授权”部分执行以下步骤：

    ![在应用端配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_004.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“注销提供程序 URL”文本框中放置 Azure AD 应用程序配置向导中**远程登录 URL** 的值。

    b.保留“数据库类型”设置，即设置为“共享”。 在“客户端 idp 目标 URL”文本框中放置 Azure AD 应用程序配置向导中**远程注销 URL** 的值。

    c. 从下载的证书中复制“指纹”值，然后将其粘贴到“客户端 idp 证书指纹”文本框中。 

    d.单击“下一步”。 单击“保存设置”。

    > [!NOTE]
    > 有关详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)

12. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。

    ![Azure AD 单一登录][10]

13. 在“单一登录确认”页上，单击“完成”。  
  
    ![Azure AD 单一登录][11]


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-officespace-tutorial/create_aaduser_09.png) 

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要显示用户列表，请在顶部菜单中，单击“用户”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-officespace-tutorial/create_aaduser_03.png) 

4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-officespace-tutorial/create_aaduser_04.png) 

5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-officespace-tutorial/create_aaduser_05.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。

    b. 在“用户名”文本框中，键入“BrittaSimon”。

    c. 单击“下一步”。

6.  在“用户配置文件”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-officespace-tutorial/create_aaduser_06.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  

    b. 在“姓氏”文本框中，键入“Simon”。

    c. 在“显示名称”文本框中，键入“Britta Simon”。

    d.单击“下一步”。 在“角色”列表中，选择“用户”。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”。

7. 在“获取临时密码”对话框页上，单击“创建”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-officespace-tutorial/create_aaduser_07.png) 

8. 在“获取临时密码”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-officespace-tutorial/create_aaduser_08.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。

    b. 单击“完成”。   



### <a name="creating-an-officespace-software-test-user"></a>创建 OfficeSpace Software 测试用户

本部分的目的是在 OfficeSpace Software 中创建名为“Britta Simon”的用户。 OfficeSpace Software 支持在默认情况下启用的实时预配。

此部分不存在任何操作项。 尝试访问 OfficeSpace Software 期间，如果该用户尚不存在，则将创建一个新用户。

> [!NOTE]
> 如果需要手动创建用户，则需联系 [OfficeSpace Software 支持团队](emaiLto:support@officespacesoftware.com)。


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分需授予 Britta Simon 访问 OfficeSpace Software 的权限，使之能够使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 OfficeSpace Software，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“OfficeSpace Software”。

    ![配置单一登录](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_50.png) 

3. 在顶部菜单中，单击“用户”。

    ![分配用户][203] 

4. 在“用户”列表中，选择“Britta Simon”。

5. 在底部工具栏中，单击“分配”。
    
    ![分配用户][205]



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

[6]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->



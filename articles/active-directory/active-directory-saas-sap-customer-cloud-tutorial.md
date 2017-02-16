---
title: "教程：Azure Active Directory 与 SAP Cloud for Customer 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 SAP Cloud for Customer 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: bec672005b0b1856faeb230f5674013a37add774


---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>教程：将 Azure Active Directory 与 SAP Cloud for Customer 集成
在本教程中，了解如何将 SAP Cloud for Customer 与 Azure Active Directory (Azure AD) 集成。

将 SAP Cloud for Customer 与 Azure AD 集成提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 SAP Cloud for Customer
* 可以让用户使用其 Azure AD 帐户自动登录到 SAP Cloud for Customer（单一登录）
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 SAP Cloud 的集成，需备齐以下项目：

* Azure AD 订阅
* 已启用 SAP Cloud for Customer 单一登录的订阅

> [!NOTE]
> 测试本教程中的步骤时，建议不要使用生产环境。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 SAP Cloud for Customer
2. 配置并测试 Azure AD 单一登录

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>从库中添加 SAP Cloud for Customer
若要配置 SAP Cloud for Customer 与 Azure AD 的集成，需要从库中将 SAP Cloud for Customer 添加到托管 SaaS 应用列表。

**若要从库中添加 SAP Cloud for Customer，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![Active Directory][1]

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]

4. 在页面底部单击“添加”。
   
    ![应用程序][3]

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]

6. 在搜索框中，键入“SAP Cloud for Customer”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_01.png)

7. 在结果窗格中，选择“SAP Cloud for Customer”，然后单击“完成”以添加该应用程序。
   
    ![Active Directory](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置并测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 SAP Cloud for Customer 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 SAP Cloud for Customer 用户。 换句话说，需要在 Azure AD 用户与 SAP Cloud for Customer 中相关用户之间建立链接关系。

若要配置和测试 SAP Cloud for Customer 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 SAP Cloud for Customer 测试用户](#creating-an-sap-business-bydesign-test-user)** - 在 SAP Cloud for Customer 中创建 Britta Simon 的对应用户，将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
在本部分中，将在经典门户中启用 Azure AD 单一登录并在 SAP Cloud for Customer 应用程序中配置单一登录。 

**若要配置 SAP Cloud for Customer 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“SAP Cloud for Customer”应用程序集成页上，单击顶部菜单中的“属性”。
   
    ![配置单一登录](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_80.png) 

2. 在属性 SAML 令牌属性列表中，选择 nameidentifier 属性，然后单击“编辑”。
   
    ![配置单一登录](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_84.png) 

3. 在“编辑用户属性”对话框中，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_85.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“属性值”列表中，选择“ExtractMailPrefix()”函数。

    b.保留“数据库类型”设置，即设置为“共享”。 从“邮件”列表中，选择要用于实现的用户属性。 
    例如，如果想要使用 EmployeeID 作为唯一用户标识符并且已在 ExtensionAttribute2 中存储属性值，则选择 user.extensionattribute2。 

    c. 单击“完成”。 


1. 在经典门户中的“SAP Cloud for Customer”应用程序集成页上，单击“配置单一登录”。
   
    ![配置单一登录][6] 

2. 在“你希望用户如何登录到 SAP Cloud for Customer”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_03.png) 

3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_04.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入用户用于登录 SAP Cloud for Customer 应用程序的 URL：`https://<server name>.crm.ondemand.com`
   
    b.保留“数据库类型”设置，即设置为“共享”。 单击“下一步”

4. 在“配置 SAP Cloud for Customer 的单一登录”页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_05.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“下载元数据”，然后在计算机上保存该文件。
   
    b. 单击“下一步”。

5. 若要配置 SSO，请执行以下步骤：
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 以管理员权限登录到 SAP Cloud for Customer 门户。
   
    b.保留“数据库类型”设置，即设置为“共享”。 导航到“应用程序和用户管理常见任务”，单击“标识提供者”选项卡。
   
    c. 单击“新建标识提供者”，并选择从 Azure 经典门户下载的元数据 XML 文件。 导入元数据时，系统会自动上载所需的签名证书和加密证书。
   
    ![配置单一登录](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    d.单击“下一步”。 Azure AD 要求在 SAML 请求中包含“断言使用者服务 URL”元素，因此请选中“包括断言使用者服务 URL”复选框。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“激活单一登录”。
   
    f. 保存所做更改。
   
    g. 单击“我的系统”选项卡。
   
    ![配置单一登录](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. 复制 **SSO URL**，并将其粘贴到“Azure AD 登录 URL”文本框中。
   
    ![配置单一登录](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    i. 指定员工是否可以手动在以下两者中选择：使用用户 ID 和密码登录，或者通过选择“手动选择标识提供者”使用 SSO 登录。
   
    j. 在“SSO URL”部分中，指定员工应该用于登录到系统的 URL。 
    在“发送给员工的 URL”列表中，可以选择下列选项：
   
    **非 SSO URL**
   
    系统仅将普通的系统 URL 发送给员工。 员工不能使用 SSO 登录，必须改用密码或证书。
   
    **SSO URL** 
   
    系统仅将 SSO URL 发送给员工。 员工可以使用 SSO 登录。 通过 IdP 重定向身份验证请求。
   
    **自动选择**
   
    如果 SSO 未处于活动状态，系统将向员工发送普通的系统 URL。 如果 SSO 处于活动状态，系统会检查员工是否有密码。 如果密码可用，系统会向员工发送 SSO URL 和非 SSO URL。 但是，如果员工没有密码，则仅向员工发送 SSO URL。
   
    k. 保存所做更改。

6. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![Azure AD 单一登录][10]

7. 在“单一登录确认”页上，单击“完成”。  
   
    ![Azure AD 单一登录][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分中，将在 Azure 经典门户中创建一个名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_09.png) 

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_03.png) 

4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_04.png) 

5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_05.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“资源组名称” 的 Azure 数据工厂。

6. 在“用户配置文件”对话框页面上，执行以下步骤： ![创建 Azure AD 测试用户](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_06.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
    b. 在“姓氏”文本框中，键入“Simon”。
   
    c. 在“显示名称”文本框中，键入“Britta Simon”。
   
    d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”。

7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_07.png) 

8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_08.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-an-sap-cloud-for-customer-test-user"></a>创建 SAP Cloud for Customer 测试用户
在本部分中，将在 SAP Cloud for Customer 中创建一个名为“Britta Simon”的用户。 请协助 SAP Cloud for Customer 支持团队将用户添加到 SAP Cloud for Customer 平台。 

> [!NOTE]
> 请确保 NameID 值应与 SAP Cloud for Customer 平台中的用户名字段匹配。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
在本部分中，通过授予 Britta Simon 访问 SAP Cloud for Customer 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 SAP Cloud for Customer，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 

2. 在应用程序列表中，选择“SAP Cloud for Customer”。
   
    ![配置单一登录](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_50.png) 

3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]

4. 在“用户”列表中，选择“Britta Simon”。

5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
在本部分中，将使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“SAP Cloud for Customer”磁贴时，用户应自动登录到 SAP Cloud for Customer 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->



---
title: "教程：Azure Active Directory 与 FreshDesk 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 FreshDesk 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 0a8f2de60ab60b980b29c4dbc78045bd20693b9d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017


---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>教程：Azure Active Directory 与 FreshDesk 集成

在本教程中，了解如何将 FreshDesk 与 Azure Active Directory (Azure AD) 集成。

将 FreshDesk 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 FreshDesk
- 可以让用户使用其 Azure AD 帐户自动登录到 FreshDesk（单一登录）
- 可在一个中心位置（即 Azure 管理门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 FreshDesk 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了 FreshDesk 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 FreshDesk
2. 配置并测试 Azure AD 单一登录

## <a name="adding-freshdesk-from-the-gallery"></a>从库中添加 FreshDesk
若要配置 FreshDesk 与 Azure AD 的集成，需要从库中将 FreshDesk 添加到托管 SaaS 应用列表。

**若要从库中添加 FreshDesk，请执行以下步骤：**

1. 在 **[Azure 管理门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“FreshDesk”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_search.png)

5. 在结果面板中，选择“FreshDesk”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 FreshDesk 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 FreshDesk 用户。 换句话说，需要建立 Azure AD 用户与 FreshDesk 中相关用户之间的关联关系。

可以通过将 Azure AD 中“用户名”的值分配为 FreshDesk 中“用户名”的值来建立此关联关系。

若要配置和测试 FreshDesk 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 FreshDesk 测试用户](#creating-a-freshdesk-test-user)**：在 FreshDesk 中创建 Britta Simon 的对应用户，将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 管理门户中启用 Azure AD 单一登录并在 FreshDesk 应用程序中配置单一登录。

**若要配置 FreshDesk 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 管理门户的“FreshDesk”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. 在“FreshDesk 域和 URL”部分，请在“登录 URL”中输入 `https://<tenant-name>.freshdesk.com` 或 FreshDesk 建议的任何其他值。

    ![配置单一登录](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_url.png)

    > [!NOTE] 
    > 请注意，这不是实际值。 必须使用实际登录 URL 更新此值。 若要获取此值，请与 [FreshDesk 客户端支持团队](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg)联系。  

4. 在“SAML 签名证书”部分中，单击“证书”，然后将证书保存在计算机上。

    ![配置单一登录](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-freshdesk-tutorial/tutorial_general_400.png)

6. 在“FreshDesk 配置”部分中，单击“配置 FreshDesk”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 单一登录服务 URL”和“注销 URL”。

    ![配置单一登录](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_configure.png)

7. 在另一个 Web 浏览器窗口中，以管理员身份登录 Freshdesk 公司站点。

8. 在顶部菜单中，单击“管理员”。
   
       ![Admin](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Admin")

9. 在“常规设置”选项卡上，单击“安全”。
   
       ![Security](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Security")

10. 在“安全”部分中，执行以下步骤：
   
    ![单一登录](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "单一登录")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“单一登录(SSO)”，请选择“启用”。

    b. 选择“SAML SSO”。

    c. 将从 Azure 门户复制的“SAML 单一登录服务 URL”键入到“SAML 登录 URL”文本框中。

    d. 将从 Azure 门户复制的“注销 URL”键入到“注销 URL”文本框中。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 从 Azure 门户的下载证书中复制“指纹”值，然后将其粘贴到“安全证书指纹”文本框中。  
 
    >[!TIP]
    >有关更多详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)。 
    
    f. 单击“保存” 。


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 管理门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建”。
 
### <a name="creating-a-freshdesk-test-user"></a>创建 FreshDesk 测试用户

为使 Azure AD 用户能够登录到 FreshDesk，必须将其预配到 FreshDesk。  
就 FreshDesk 而言，预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到“Freshdesk”租户。
2. 在顶部菜单中，单击“管理员”。
   
   ![管理员](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "管理员")

3. 在“常规设置”选项卡上，单击“代理”。
   
   ![代理](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agents")

4. 单击“新建代理”。
   
    ![新建代理](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "New Agent")

5. 在“代理信息”对话框中，执行以下步骤：
   
   ![代理信息](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Agent Information")
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“完整名称”文本框中，键入要预配的 Azure AD 帐户的名称。

   b. 在“电子邮件”文本框中，键入要预配的 Azure AD 帐户的 Azure AD 电子邮件地址。

   c. 在“名称”文本框中，键入要预配的 Azure AD 帐户的名称。

   d. 选择“代理角色”，然后单击“分配”。
       
   e. 单击“保存” 。     
   
    >[!NOTE]
    >Azure AD 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。 
    > 
    
    >[!NOTE]
    >可以使用任何其他 Freshdesk 用户帐户创建工具或 Freshdesk 提供的 API 来预配 AAD 用户帐户。 到 FreshDesk。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Box 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 FreshDesk，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“FreshDesk”。

    ![配置单一登录](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 FreshDesk 磁贴时，应该会显示登录页面，可通过该页面登录到 FreshDesk 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_203.png



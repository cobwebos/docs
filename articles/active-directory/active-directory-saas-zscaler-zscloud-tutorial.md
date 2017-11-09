---
title: "教程：Azure Active Directory 与 Zscaler ZSCloud 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 与 Zscaler ZSCloud 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: 2b6eb113e5725260bc04f50e9218939bf28b1ff0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>教程：Azure Active Directory 与 Zscaler ZSCloud 的集成

本教程介绍如何将 Zscaler ZSCloud 与 Azure Active Directory (Azure AD) 集成。

将 Zscaler ZSCloud 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Zscaler ZSCloud
- 可以让用户通过其 Azure AD 帐户自动登录到 Zscaler ZSCloud（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Zscaler ZSCloud 的集成，需要提供以下项：

- 一个 Azure AD 订阅
- 已启用 Zscaler ZSCloud 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Zscaler ZSCloud
2. 配置和测试 Azure AD 单一登录

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>从库添加 Zscaler ZSCloud
若要配置 Zscaler ZSCloud 与 Azure AD 的集成，需要从库中将 Zscaler ZSCloud 添加到托管 SaaS 应用列表。

**若要从库添加 Zscaler ZSCloud，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 **Zscaler ZSCloud**。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_search.png)

5. 在结果窗格中选择“Zscaler ZSCloud”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分，根据名为“Britta Simon”的测试用户的指示配置和测试 Zscaler ZSCloud 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Zscaler ZSCloud 用户。 换句话说，需要在 Azure AD 用户与 Zscaler ZSCloud 中的相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 Zscaler ZSCloud 中“用户名”的值来建立此链接关系。

若要配置和测试 Zscaler ZSCloud 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. [配置代理设置](#configuring-proxy-settings) - 以在 Internet Explorer 中配置代理设置
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Zscaler ZSCloud 测试用户](#creating-a-zscaler-zscloud-test-user)** - 在 Zscaler ZSCloud 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，将在 Azure 门户中启用 Azure AD 单一登录并在 Zscaler ZSCloud 应用程序中配置单一登录。

**若要配置 Zscaler ZSCloud 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Zscaler ZSCloud”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_samlbase.png)

3. 在“Zscaler ZSCloud 域和 URL”部分中执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_url.png)

     在“登录 URL”文本框中，键入用户用来登录 ZScaler ZSCloud 应用程序的 URL。
    
    > [!NOTE] 
    > 必须使用实际登录 URL 更新此值。 若要获取此值，请与 [Zscaler ZSCloud 客户端支持团队](https://support.zscaler.com/hc/articles/210172606-Zscaler-is-Expanding-Its-Zscloud-Global-Footprint)联系。 
 
4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_400.png)

6. 在“Zscaler ZSCloud 配置”部分中，单击“配置 Zscaler ZSCloud”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 单一登录服务 URL”

    ![配置单一登录](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_configure.png) 

7. 在其他 Web 浏览器窗口中，以管理员身份登录到 ZScaler ZSCloud 公司站点。

8. 在顶部菜单中，单击“管理”。
   
    ![管理](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800206.png "Administration")

9. 在“管理管理员和角色”下，单击“管理用户和身份验证”。   
            
    ![管理用户和身份验证](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800207.png "管理用户和身份验证")

10. 在“选择组织的身份验证选项”部分中，执行以下步骤：   
                
    ![身份验证](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800208.png "身份验证")
   
    a. 选择“使用 SAML 单一登录进行身份验证”。

    b. 单击“配置 SAML 单一登录参数”。

11. 在“配置 SAML 单一登录参数”对话框页上，执行以下步骤，然后单击“完成”

    ![单一登录](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800209.png "单一登录")
    
    a. 将“SAML 单一登录服务 URL”值粘贴到“发送给用户进行身份验证的 SAML 门户 URL”文本框中。
    
    b. 在“属性包含登录名”文本框中，键入 **NameID** 。
    
    c. 若要上传已下载的证书，请单击“Zscaler pem”。
    
    d. 选择“启用 SAML 自动预配”。

12. 在“配置用户身份验证”对话框页上，执行以下步骤：

    ![管理](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800210.png "Administration")
    
    a. 单击“保存” 。

    b. 单击“立即激活”。

## <a name="configuring-proxy-settings"></a>配置代理设置
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>在 Internet Explorer 中配置代理设置

1. 启动 **Internet Explorer**。

2. 从“工具”菜单中选择“Internet 选项”，打开“Internet 选项”对话框。   
    
     ![Internet 选项](./media/active-directory-saas-zscaler-zscloud-tutorial/ic769492.png "Internet 选项")

3. 单击“连接”选项卡。   
  
     ![连接](./media/active-directory-saas-zscaler-zscloud-tutorial/ic769493.png "连接")

4. 单击“LAN 设置”，打开“LAN 设置”对话框。

5. 在“代理服务器”部分中，执行以下步骤：   
   
    ![代理服务器](./media/active-directory-saas-zscaler-zscloud-tutorial/ic769494.png "代理服务器")

    a. 选择“为 LAN 使用代理服务器”。

    b. 在“地址”文本框中，键入 **gateway.zscalerone.net**。

    c. 在“端口”文本框中，键入 **80**。

    d. 选择“对本地地址不使用代理服务器”。

    e. 单击“确定”，关闭“局域网(LAN)设置”对话框。

6. 单击“确定”，关闭“Internet 选项”对话框。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。

### <a name="creating-a-zscaler-zscloud-test-user"></a>创建 Zscaler ZSCloud 测试用户

若要让 Azure AD 用户登录到 ZScaler ZSCloud，必须将其预配到 ZScaler ZSCloud。  
对于 ZScaler ZSCloud，需要手动执行预配。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：

1. 登录到 **Zscaler** 租户。

2. 单击“管理”。   
   
    ![管理](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781035.png "Administration")

3. 单击“用户管理”。   
        
     ![添加](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781037.png "Add")

4. 在“用户”选项卡上，单击“添加”。
      
    ![添加](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781037.png "Add")

5. 在“添加用户”部分中，执行以下步骤：
        
    ![添加用户](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781038.png "添加用户")
   
    a. 键入“UserID”、“用户显示名称”、“密码”和“确认密码”，并选择想要预配的有效 AAD 帐户的“组”和“部门”。

    b. 单击“保存”。

> [!NOTE]
> 可以使用 ZScaler ZSCloud 提供的任何其他 ZScaler ZSCloud 用户帐户创建工具或 API 来预配 AAD 用户帐户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，通过授予 Britta Simon 访问 Zscaler ZSCloud 的权限，使其能够使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Zscaler ZSCloud，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Zscaler ZSCloud”。

    ![配置单一登录](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

如果要测试单一登录设置，请打开访问面板。

在访问面板中单击 Zscaler ZSCloud 磁贴时，应会自动登录到 Zscaler ZSCloud 应用程序。

有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_203.png


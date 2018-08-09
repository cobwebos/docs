---
title: 教程：Azure Active Directory 与 Zscaler 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Zscaler 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 68ddeefd9cbfadb3081885819506b6e916d03354
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446793"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler"></a>教程：Azure Active Directory 与 Zscaler 的集成

本教程介绍如何将 Zscaler 与 Azure Active Directory (Azure AD) 集成。

将 Zscaler 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Zscaler
- 可以让用户通过其 Azure AD 帐户自动登录 Zscaler（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Zscaler 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Zscaler 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Zscaler
1. 配置和测试 Azure AD 单一登录

## <a name="adding-zscaler-from-the-gallery"></a>从库中添加 Zscaler
若要配置 Zscaler 与 Azure AD 的集成，需从库中将 Zscaler 添加到托管 SaaS 应用列表。

若要从库中添加 Zscaler，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

1. 在“搜索框”中，键入 Zscaler。

    ![创建 Azure AD 测试用户](./media/zscaler-tutorial/tutorial_zscaler_search.png)

1. 在结果面板中，选择“Zscaler”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/zscaler-tutorial/tutorial_zscaler_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Zscaler 的 Azure AD 单一登录。

为了使单一登录能正常工作，Azure AD 需要知道 Zscaler 中与 Azure AD 用户相对应的用户。 换句话说，需要在 Azure AD 用户与 Zscaler 中的相关用户间建立关联。

将 Azure AD 中“用户名”的值指定为 Zscaler 中“用户名”的值来建立此关联。

若要配置和测试 Zscaler 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
1. [配置代理设置](#configuring-proxy-settings) - 以在 Internet Explorer 中配置代理设置
1. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. [创建 Zscaler 测试用户](#creating-a-zscaler-test-user) - 在 Zscaler 中创建 Britta Simon 的对应用户，其与用户的 Azure AD 表示形式相关联。
1. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
1. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Zscaler 应用程序中配置单一登录。

若要配置 Zscaler 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的 Zscaler 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/zscaler-tutorial/tutorial_zscaler_samlbase.png)

1. 在“Zscaler 域和 URL”部分，执行以下步骤：

    ![配置单一登录](./media/zscaler-tutorial/tutorial_zscaler_url.png)

    在“登录 URL”文本框中，使用以下模式键入 URL： `https://<companyname>.zsclaer.net`

    > [!NOTE] 
    > 此值不是真实值。 使用实际登录 URL 更新此值。 请联系 [Zscaler 客户端支持团队](https://www.zscaler.com/company/contact)获取此值。 

1. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/zscaler-tutorial/tutorial_zscaler_certificate.png) 

1. 单击“保存”按钮。

    ![配置单一登录](./media/zscaler-tutorial/tutorial_general_400.png)

1. 在“Zscaler 配置”部分，单击“配置 Zscaler”，打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 单一登录服务 URL”

    ![配置单一登录](./media/zscaler-tutorial/tutorial_zscaler_configure.png) 

1. 在另一 Web 浏览器窗口中，以管理员身份登录 ZScaler 公司站点。

1. 在顶部菜单中，单击“管理”。
   
    ![管理](./media/zscaler-tutorial/ic800206.png "Administration")

1. 在“管理管理员和角色”下，单击“管理用户和身份验证”。   
            
    ![管理用户和身份验证](./media/zscaler-tutorial/ic800207.png "管理用户和身份验证")

1. 在“选择组织的身份验证选项”部分中，执行以下步骤：   
                
    ![身份验证](./media/zscaler-tutorial/ic800208.png "身份验证")
   
    a. 选择“使用 SAML 单一登录进行身份验证”。

    b. 单击“配置 SAML 单一登录参数”。

1. 在“配置 SAML 单一登录参数”对话框页上，执行以下步骤，然后单击“完成”

    ![单一登录](./media/zscaler-tutorial/ic800209.png "单一登录")
    
    a. 将从 Azure 门户复制的“SAML 单一登录服务 URL”值粘贴到“发送给用户进行身份验证的 SAML 门户 URL”文本框中。
    
    b. 在“属性包含登录名”文本框中，键入 **NameID** 。
    
    c. 若要上传已下载的证书，请单击“Zscaler pem”。
    
    d. 选择“启用 SAML 自动预配”。

1. 在“配置用户身份验证”对话框页上，执行以下步骤：

    ![管理](./media/zscaler-tutorial/ic800210.png "Administration")
    
    a. 单击“ **保存**”。

    b. 单击“立即激活”。

## <a name="configuring-proxy-settings"></a>配置代理设置
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>在 Internet Explorer 中配置代理设置

1. 启动 **Internet Explorer**。

1. 从“工具”菜单中选择“Internet 选项”，打开“Internet 选项”对话框。   
    
     ![Internet 选项](./media/zscaler-tutorial/ic769492.png "Internet 选项")

1. 单击“连接”选项卡。   
  
     ![连接](./media/zscaler-tutorial/ic769493.png "连接")

1. 单击“LAN 设置”，打开“LAN 设置”对话框。

1. 在“代理服务器”部分中，执行以下步骤：   
   
    ![代理服务器](./media/zscaler-tutorial/ic769494.png "代理服务器")

    a. 选择“为 LAN 使用代理服务器”。

    b. 在“地址”文本框中，键入 gateway.zscaler.net。

    c. 在“端口”文本框中，键入 **80**。

    d. 选择“对本地地址不使用代理服务器”。

    e. 单击“确定”，关闭“局域网(LAN)设置”对话框。

1. 单击“确定”，关闭“Internet 选项”对话框。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/zscaler-tutorial/create_aaduser_01.png) 

1. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/zscaler-tutorial/create_aaduser_02.png) 

1. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/zscaler-tutorial/create_aaduser_03.png) 

1. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/zscaler-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-zscaler-test-user"></a>创建 Zscaler 测试用户

为使 Azure AD 用户能够登录 ZScaler，必须将用户预配到 ZScaler。  
对于 ZScaler，需手动执行预配。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：

1. 登录到 **Zscaler** 租户。

1. 单击“管理”。   
   
    ![管理](./media/zscaler-tutorial/ic781035.png "Administration")

1. 单击“用户管理”。   
        
     ![添加](./media/zscaler-tutorial/ic781036.png "Add")

1. 在“用户”选项卡上，单击“添加”。
      
    ![添加](./media/zscaler-tutorial/ic781037.png "Add")

1. 在“添加用户”部分中，执行以下步骤：
        
    ![添加用户](./media/zscaler-tutorial/ic781038.png "添加用户")
   
    a. 键入“UserID”、“用户显示名称”、“密码”和“确认密码”，并选择想要预配的有效 AAD 帐户的“组”和“部门”。

    b. 单击“ **保存**”。

> [!NOTE]
> 可使用任何其他 ZScaler 用户帐户创建工具或 ZScaler 提供的 API 来预配 AAD 用户帐户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分中，通过授予 Britta Simon 访问 Zscaler 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 Zscaler，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“Zscaler”。

    ![配置单一登录](./media/zscaler-tutorial/tutorial_zscaler_app.png) 

1. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Zscaler 磁贴时，会自动登录 Zscaler 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-tutorial/tutorial_general_203.png


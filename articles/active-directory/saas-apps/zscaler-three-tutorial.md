---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Zscaler Three | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Zscaler Three 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b23abcf9a39ce7f6d77bc40e7143505bc68e8b72
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72554982"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-three"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Zscaler Three 集成

本教程介绍如何将 Zscaler Three 与 Azure Active Directory (Azure AD) 集成。 将 Zscaler Three 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Zscaler Three。
* 让用户使用其 Azure AD 帐户自动登录到 Zscaler Three。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Zscaler Three 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Zscaler Three 支持 SP 发起的 SSO 

* Zscaler Three 支持实时用户预配 

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-zscaler-three-from-the-gallery"></a>从库添加 Zscaler Three

若要配置 Zscaler Three 与 Azure AD 的集成，需从库中将 Zscaler Three 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Zscaler Three”   。
1. 从结果面板中选择“Zscaler Three”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-three"></a>为 Zscaler Three 配置和测试 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置和测试 Zscaler Three 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Zscaler Three 相关用户之间建立链接关系。

若要配置和测试 Zscaler Three 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Zscaler Three SSO](#configure-zscaler-three-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Zscaler Three 测试用户](#create-zscaler-three-test-user)** - 在 Zscaler Three 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Zscaler Three”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    在“登录 URL”文本框中，键入 URL：`https://login.zscalerthree.net/sfc_sso` 

1. Zscaler Three 应用程序需要特定格式的 SAML 断言，这要求向“SAML 令牌属性”配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/edit-attribute.png)

6. 除了上述属性，Zscaler Three 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | Name | 源属性 |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    > [!NOTE]
    > 若要了解如何在 Azure AD 中配置角色，请单击[此处](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Zscaler Three”部分中，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 B.Simon 访问 Zscaler Three 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Zscaler Three”  。
1. 在“用户和组”对话框中，从列表中选择用户（例如“Britta Simon”），然后单击屏幕底部的“选择”按钮    。

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

1. 从“选择角色”对话框中，选择列表中合适的用户角色，然后单击屏幕底部的“选择”按钮   。

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

1. 在“添加分配”  对话框中，选择“分配”  按钮。

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

## <a name="configure-zscaler-three-sso"></a>配置 Zscaler Three SSO

1. 若要在“Zscaler Three”中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展”   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“安装 Zscaler Three”  会将你定向到 Zscaler Three 应用程序。 在此处，请提供管理员凭据以登录到 Zscaler Three。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置](common/setup-sso.png)

3. 若要手动设置 Zscaler Three，请打开新的 Web 浏览器窗口，以管理员身份登录 Zscaler Three 公司网站，并执行以下步骤：

4. 转到“管理”>“身份验证”>“身份验证设置”并执行以下步骤： 
   
    ![管理](./media/zscaler-three-tutorial/ic800206.png "管理")

    a. 在“身份验证类型”下选择“SAML”。 

    b. 单击“配置 SAML”。 

5. 在“编辑 SAML”窗口中，执行以下步骤并单击“保存”。   
            
    ![管理用户和身份验证](./media/zscaler-three-tutorial/ic800208.png "管理用户和身份验证")
    
    a. 在“SAML 门户 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”   。

    b. 在“登录名属性”文本框中，输入 **NameID**。 

    c. 单击“上传”，以上传从 Azure 门户的“公共 SSL 证书”中下载的 Azure SAML 签名证书。  

    d. 切换“启用 SAML 自动预配”  。

    e. 若要为 displayName 属性启用 SAML 自动预配，请在“用户显示名称属性”文本框中输入 **displayName**。 

    f. 若要为 memberOf 属性启用 SAML 自动预配，请在“组名称属性”文本框中输入 **memberOf**。 

    g. 若要为 department 属性启用 SAML 自动预配，请在“部门名称属性”中输入 **department**。 

    h. 单击“ **保存**”。

6. 在“配置用户身份验证”  对话框页上，执行以下步骤：

    ![管理](./media/zscaler-three-tutorial/ic800207.png)

    a. 将鼠标悬停在左下角附近的“激活”菜单上。 

    b. 单击“激活”  。

## <a name="configuring-proxy-settings"></a>配置代理设置
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>在 Internet Explorer 中配置代理设置

1. 启动 **Internet Explorer**。

2. 从“工具”菜单中选择“Internet 选项”，打开“Internet 选项”对话框。      
    
     ![Internet 选项](./media/zscaler-three-tutorial/ic769492.png "Internet 选项")

3. 单击“连接”  选项卡。   
  
     ![连接](./media/zscaler-three-tutorial/ic769493.png "连接")

4. 单击“LAN 设置”  ，打开“LAN 设置”  对话框。

5. 在“代理服务器”部分中，执行以下步骤：   
   
    ![代理服务器](./media/zscaler-three-tutorial/ic769494.png "代理服务器")

    a. 选择“为 LAN 使用代理服务器”。 

    b. 在“地址”文本框中，键入 **gateway.Zscaler Three.net**。

    c. 在“端口”文本框中，键入 **80**。

    d. 选择“对本地地址不使用代理服务器”  。

    e. 单击“确定”  ，关闭“局域网(LAN)设置”  对话框。

6. 单击“确定”  ，关闭“Internet 选项”  对话框。

### <a name="create-zscaler-three-test-user"></a>创建 Zscaler Three 测试用户

在本部分，我们将在 Zscaler Three 中创建一个名为 B.Simon 的用户。 Zscaler Three 支持在默认情况下启用的实时预配。 此部分不存在任何操作项。 尝试访问 Zscaler Three 时，如果 Zscaler Three 中尚不存在用户，则系统会创建一个新用户。

>[!Note]
>如果需要手动创建用户，请联系 [Zscaler Three 支持团队](https://www.zscaler.com/company/contact)。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Zscaler Three 磁贴时，应当会自动登录到为其设置了 SSO 的 Zscaler Three。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Zscaler Three](https://aad.portal.azure.com/)


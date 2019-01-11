---
title: 教程：Azure Active Directory 与 Zscaler Three 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Zscaler Three 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: jeedes
ms.openlocfilehash: 442d7478669a206f04cb799cb86b807cfe7b0624
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790144"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>教程：Azure Active Directory 与 Zscaler Three 的集成

本教程介绍如何将 Zscaler Three 与 Azure Active Directory (Azure AD) 集成。

将 Zscaler Three 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Zscaler Three。
- 可以让用户使用其 Azure AD 帐户自动登录到 Zscaler Three（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Zscaler Three 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Zscaler Three 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Zscaler Three
2. 配置和测试 Azure AD 单一登录

## <a name="adding-zscaler-three-from-the-gallery"></a>从库添加 Zscaler Three

若要配置 Zscaler Three 与 Azure AD 的集成，需从库中将 Zscaler Three 添加到托管 SaaS 应用列表。

**若要从库添加 Zscaler Three，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Zscaler Three”，在结果面板中选择“Zscaler Three”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Zscaler Three](./media/zscaler-three-tutorial/tutorial_zscalerthree_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Zscaler Three 的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道 Zscaler Three 中与 Azure AD 用户相对应的用户。 换句话说，需要建立起 Azure AD 用户与 Zscaler Three 中的相关用户之间的关联。

若要配置和测试 Zscaler Three 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Zscaler Three 单一登录](#configure-zscaler-three-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[创建 Zscaler Three 测试用户](#create-zscaler-three-test-user)** - 在 Cisco Umbrella 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
5. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Zscaler Three 应用程序中配置单一登录。

**若要配置 Zscaler Three 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Zscaler Three”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![配置单一登录](common/tutorial_general_301.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![配置单一登录](common/editconfigure.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![Zscaler Three 域和 URL 单一登录信息](./media/zscaler-three-tutorial/tutorial_zscalerthree_url.png)

    在“登录 URL”文本框中，键入 URL `https://login.zscalerthree.net/sfc_sso`

5. Zscaler Three 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性和声明”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮打开“用户属性和声明”对话框。

    ![“属性”链接](./media/zscaler-three-tutorial/tutorial_zscalerthree_attribute.png)

6. 在“用户属性”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    | 名称  | 源属性  |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](./common/new_save_attribute.png)
    
    ![图像](./common/new_attribute_details.png)

    b. 在“源属性”列表中，选择属性值。

    c. 单击“确定” 。

    d. 单击“ **保存**”。

    > [!NOTE]
    > 若要了解如何在 Azure AD 中配置角色，请单击[此处](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)

7. 在“SAML 签名证书”页的“SAML 签名证书”部分中，单击“下载”以下载“证书(Base64)”并将证书文件保存在计算机上。

    ![证书下载链接](./media/zscaler-three-tutorial/tutorial_zscalerthree_certificate.png) 

8. 在“设置 Zscaler Three”部分中，根据需求复制相应的 URL。

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    ![Zscaler Three 配置](common/configuresection.png)

### <a name="configure-zscaler-three-single-sign-on"></a>配置 Zscaler Three 单一登录

9. 在其他 Web 浏览器窗口中，以管理员身份登录到 Zscaler Three 公司站点。

10. 转到“管理”>“身份验证”>“身份验证设置”并执行以下步骤：
   
    ![管理](./media/zscaler-three-tutorial/ic800206.png "Administration")

    a. 在“身份验证类型”下选择“SAML”。

    b. 单击“配置 SAML”。

11. 在“编辑 SAML”窗口中，执行以下步骤并单击“保存”。  
            
    ![管理用户和身份验证](./media/zscaler-three-tutorial/ic800208.png "管理用户和身份验证")
    
    a. 在“SAML 门户 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”。

    b. 在“登录名属性”文本框中，输入 **NameID**。

    c. 单击“上传”，以上传从 Azure 门户的“公共 SSL 证书”中下载的 Azure SAML 签名证书。

    d. 切换“启用 SAML 自动预配”。

    e. 若要为 displayName 属性启用 SAML 自动预配，请在“用户显示名称属性”文本框中输入 **displayName**。

    f. 若要为 memberOf 属性启用 SAML 自动预配，请在“组名称属性”文本框中输入 **memberOf**。

    g. 若要为 department 属性启用 SAML 自动预配，请在“部门名称属性”中输入 **department**。

    i. 单击“ **保存**”。

12. 在“配置用户身份验证”对话框页上，执行以下步骤：

    ![管理](./media/zscaler-three-tutorial/ic800207.png)

    a. 将鼠标悬停在左下角附近的“激活”菜单上。

    b. 单击“激活”。

## <a name="configuring-proxy-settings"></a>配置代理设置
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>在 Internet Explorer 中配置代理设置

1. 启动 **Internet Explorer**。

1. 从“工具”菜单中选择“Internet 选项”，打开“Internet 选项”对话框。   
    
     ![Internet 选项](./media/zscaler-three-tutorial/ic769492.png "Internet 选项")

1. 单击“连接”选项卡。   
  
     ![连接](./media/zscaler-three-tutorial/ic769493.png "连接")

1. 单击“LAN 设置”，打开“LAN 设置”对话框。

1. 在“代理服务器”部分中，执行以下步骤：   
   
    ![代理服务器](./media/zscaler-three-tutorial/ic769494.png "代理服务器")

    a. 选择“为 LAN 使用代理服务器”。

    b. 在“地址”文本框中，键入 **gateway.Zscaler Three.net**。

    c. 在“端口”文本框中，键入 **80**。

    d. 选择“对本地地址不使用代理服务器”。

    e. 单击“确定”，关闭“局域网(LAN)设置”对话框。

1. 单击“确定”，关闭“Internet 选项”对话框。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](common/create_aaduser_01.png) 

3. 在“用户属性”中，按照以下步骤操作。

    ![创建 Azure AD 测试用户](common/create_aaduser_02.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="create-zscaler-three-test-user"></a>创建 Zscaler Three 测试用户

本部分的目的是在 Zscaler Three 中创建名为“Britta Simon”的用户。 Zscaler Three 支持在默认情况下启用的实时预配。 此部分不存在任何操作项。 尝试访问 Zscaler Three 期间，如果该用户尚不存在，则将创建一个新用户。
>[!Note]
>如需手动创建用户，请联系  [Zscaler Three 支持团队](https://www.zscaler.com/company/contact)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，通过授予 Britta Simon 访问 Zscaler Three 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“Zscaler Three”。

    ![配置单一登录](./media/zscaler-three-tutorial/tutorial_zscalerthree_app.png)

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框中，从列表中选择用户（例如“Britta Simon”），然后单击屏幕底部的“选择”按钮。

    ![图像](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

6. 从“选择角色”对话框中，选择列表中合适的用户角色，然后单击屏幕底部的“选择”按钮。

    ![图像](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

7. 在“添加分配”对话框中，选择“分配”按钮。

    ![图像](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Zscaler Three 磁贴时，应会自动登录到 Zscaler Three 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png

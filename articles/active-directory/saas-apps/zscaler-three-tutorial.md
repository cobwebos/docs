---
title: 教程：Azure Active Directory 与 Zscaler Three 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Zscaler Three 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.openlocfilehash: 0ef8fc2ea8b006d49dd54d638183a58bf78a5797
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311682"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>教程：Azure Active Directory 与 Zscaler Three 集成

本教程介绍如何将 Zscaler Three 与 Azure Active Directory (Azure AD) 集成。

将 Zscaler Three 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Zscaler Three
- 可让用户通过其 Azure AD 帐户自动登录到 Zscaler Three（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Zscaler Three 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Zscaler Three 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可在此处获取一个月的试用版：[试用产品/服务](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。
本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Zscaler Three
2. 配置和测试 Azure AD 单一登录

## <a name="adding-zscaler-three-from-the-gallery"></a>从库添加 Zscaler Three

若要配置 Zscaler Three 与 Azure AD 的集成，需从库中将 Zscaler Three 添加到托管 SaaS 应用列表。

**若要从库添加 Zscaler Three，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![Active Directory][1]

2. 转到“企业应用”，并选择“所有应用”选项。

    ![应用程序][2]

3. 单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在结果面板中，选择“Zscaler Three”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/zscaler-three-tutorial/tutorial_zscalerthree_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Zscaler Three 的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道 Zscaler Three 中与 Azure AD 用户相对应的用户。 换句话说，需要建立起 Azure AD 用户与 Zscaler Three 中的相关用户之间的关联。

若要配置和测试 Zscaler Three 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. [配置代理设置](#configuring-proxy-settings) - 以在 Internet Explorer 中配置代理设置
3. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. [创建 Zscaler Three 测试用户](#creating-a-zscaler-three-test-user) - 在 Zscaler Three 中创建 Britta Simon 的对应用户，其与用户的 Azure AD 表示形式相关联。
5. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
6. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Zscaler Three 应用程序中配置单一登录。

**若要配置 Zscaler Three 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Zscaler Three”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“选择单一登录方法”对话框中，选择 SAML/WS-Fed 模式以启用单一登录。

    ![配置单一登录](./media/zscaler-three-tutorial/tutorial_general_301.png)

3. 如果你需要从任何其他模式更改为 SAML 模式，请单击屏幕顶部的“更改单一登录模式”。

    ![配置单一登录](./media/zscaler-three-tutorial/tutorial_general_300.png)

4. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![配置单一登录](./media/zscaler-three-tutorial/tutorial_general_302.png)

5. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![配置单一登录](./media/zscaler-three-tutorial/tutorial_zscalerthree_url.png)

    在“回复 URL”文本框中，输入 URL：`https://login.zscalerthree.net/sfc_sso`

    > [!NOTE]
    > 需使用实际登录 URL 更新此值。 请联系 [Zscaler Three 客户端支持团队](https://www.zscaler.com/company/contact)来获取这些值。

6. 在“SAML 签名证书”部分中，单击“下载”以下载“证书(Base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/zscaler-three-tutorial/tutorial_zscalerthree_certificate.png)

8. 在“设置 Zscaler Three”部分中，复制“登录 URL”。

    ![配置单一登录](./media/zscaler-three-tutorial/tutorial_zscalerthree_configure.png)

9. 在其他 Web 浏览器窗口中，以管理员身份登录到 Zscaler Three 公司站点。

10. 在顶部菜单中，单击“管理”。

    ![管理](./media/zscaler-three-tutorial/ic800206.png "Administration")

9. 在“管理管理员和角色”下，单击“管理用户和身份验证”。

    ![管理用户和身份验证](./media/zscaler-three-tutorial/ic800207.png "管理用户和身份验证")

10. 在“选择组织的身份验证选项”部分中，执行以下步骤：

    ![身份验证](./media/zscaler-three-tutorial/ic800208.png "身份验证")

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 选择“使用 SAML 单一登录进行身份验证”。

    b. 单击“配置 SAML 单一登录参数”。

11. 在“配置 SAML 单一登录参数”对话框页上，执行以下步骤，然后单击“完成”

    ![单一登录](./media/zscaler-three-tutorial/ic800209.png "单一登录")

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 将从 Azure 门户复制的“登录 URL”值粘贴到“发送给用户进行身份验证的 SAML 门户 URL”文本框中。

    b. 在“属性包含登录名”文本框中，键入 **NameID** 。

    c. 若要上传已下载的证书，请单击“Zscaler pem”。

    d. 选择“启用 SAML 自动预配”。

12. 在“配置用户身份验证”对话框页上，执行以下步骤：

    ![管理](./media/zscaler-three-tutorial/ic800210.png "Administration")

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“ **保存**”。

    b. 单击“立即激活”。

## <a name="configuring-proxy-settings"></a>配置代理设置

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>在 Internet Explorer 中配置代理设置

1. 启动 **Internet Explorer**。

2. 从“工具”菜单中选择“Internet 选项”，打开“Internet 选项”对话框。

     ![Internet 选项](./media/zscaler-three-tutorial/ic769492.png "Internet 选项")

3. 单击“连接”选项卡。
  
     ![连接](./media/zscaler-three-tutorial/ic769493.png "连接")

4. 单击“LAN 设置”，打开“LAN 设置”对话框。

5. 在“代理服务器”部分中，执行以下步骤：

    ![代理服务器](./media/zscaler-three-tutorial/ic769494.png "代理服务器")

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 选择“为 LAN 使用代理服务器”。

    b. 在“地址”文本框中，键入 gateway.zscalerthree.net。

    c. 在“端口”文本框中，键入 **80**。

    d. 选择“对本地地址不使用代理服务器”。

    e. 单击“确定”，关闭“局域网(LAN)设置”对话框。

6. 单击“确定”，关闭“Internet 选项”对话框。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](./media/zscaler-three-tutorial/create_aaduser_01.png) 

3. 在“用户属性”中，按照以下步骤操作。

    ![创建 Azure AD 测试用户](./media/zscaler-three-tutorial/create_aaduser_02.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="creating-a-zscaler-three-test-user"></a>创建 Zscaler Three 测试用户

为使 Azure AD 用户能够登录到 Zscaler Three，必须将其预配到 Zscaler Three。 对于 Zscaler Three，需要手动执行预配。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：

1. 登录到 Zscaler Three 租户。

2. 单击“管理”。

    ![管理](./media/zscaler-three-tutorial/ic781035.png "Administration")

3. 单击“用户管理”。

     ![添加](./media/zscaler-three-tutorial/ic781036.png "Add")

4. 在“用户”选项卡上，单击“添加”。

    ![添加](./media/zscaler-three-tutorial/ic781037.png "Add")

5. 在“添加用户”部分中，执行以下步骤：

    ![添加用户](./media/zscaler-three-tutorial/ic781038.png "添加用户")

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 键入“UserID”、“用户显示名称”、“密码”和“确认密码”，然后选择想要预配的有效 Azure AD 帐户的“组”和“部门”。

    b. 单击“ **保存**”。

> [!NOTE]
> 可使用任何其他 Zscaler Three 用户帐户创建工具或使用 Zscaler Three 提供的 API 来预配 Azure Active Directory 用户帐户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，通过授予 Britta Simon 访问 Zscaler Three 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“Zscaler Three”。

    ![配置单一登录](./media/zscaler-three-tutorial/tutorial_zscalerthree_app.png)

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Zscaler Three 磁贴时，应会自动登录到 Zscaler Three 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-three-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-three-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-three-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-three-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-three-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-three-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-three-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-three-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-three-tutorial/tutorial_general_203.png
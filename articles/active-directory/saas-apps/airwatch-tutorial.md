---
title: 教程：Azure Active Directory 与 AirWatch 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 AirWatch 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f0a684c6a38ba3a95438941f668b36b23d278df
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227605"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>教程：将 AirWatch 与 Azure Active Directory 集成

在本教程中，了解如何将 AirWatch 与 Azure Active Directory (Azure AD) 集成。 将 AirWatch 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 AirWatch。
* 让用户使用其 Azure AD 帐户自动登录到 AirWatch。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以从[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月免费试用版。
* 已启用 AirWatch 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 AirWatch 支持 SP 发起的 SSO。

## <a name="adding-airwatch-from-the-gallery"></a>从库添加 AirWatch

若要配置 AirWatch 与 Azure AD 的集成，需要将库中的 AirWatch 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序”。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“AirWatch”。
1. 从结果面板中选择“AirWatch”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 AirWatch 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 AirWatch 相关用户之间建立链接关系。

若要配置和测试 AirWatch 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. **[配置 AirWatch SSO](#configure-airwatch-sso)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. [创建 AirWatch 测试用户](#create-airwatch-test-user) - 在 AirWatch 中创建 Britta Simon 的对应用户，将其链接到该用户的 Azure AD 表示形式。
5. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“AirWatch”应用程序集成页上，找到“管理”部分，选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML”。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”页上，输入以下字段的值：

    1. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. 在“标识符(实体 ID)”文本框中，键入值：`AirWatch`

    > [!NOTE]
    > 此值不是真实值。 使用实际登录 URL 更新此值。 若要获取此值，请与 [AirWatch 客户端支持团队](https://www.air-watch.com/company/contact-us/)联系。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. AirWatch 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性”对话框。

    ![image](common/edit-attribute.png)

1. 在“用户属性”对话框的“用户声明”部分中，通过使用“编辑图标”编辑声明或使用“添加新声明”添加声明，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    | Name |  源属性|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“添加新声明”以打开“管理用户声明”对话框。

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 将“命名空间”留空。

    d. 选择“源”作为“属性”。

    e. 在“源属性”列表中，键入为该行显示的属性值。

    f. 单击“确定”

    g. 单击“ **保存**”。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该元数据 XML 并将其保存在计算机上。

   ![证书下载链接](common/metadataxml.png)

1. 在“设置 AirWatch”部分中，根据要求复制相应 URL。

   ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>配置 AirWatch SSO

1. 在其他 Web 浏览器窗口中，以管理员身份登录 AirWatch 公司站点。

1. 在设置页上： 选择“设置”>“企业集成”>“目录服务”。

   ![设置](./media/airwatch-tutorial/ic791921.png "设置")

1. 单击“用户”选项卡，在“基 DN”文本框中键入域名，然后单击“保存”。

   ![用户](./media/airwatch-tutorial/ic791922.png "用户")

1. 单击“服务器”选项卡。

   ![服务器](./media/airwatch-tutorial/ic791923.png "服务器")

1. 在“LDAP”部分执行以下步骤：

    ![上传](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. 对于“目录类型”，选择“无”。

    b. 选择“将 SAML 用于身份验证”。

1. 在“SAML 2.0”部分，若要上传下载的证书，请单击“上传”。

    ![上传](./media/airwatch-tutorial/ic791932.png "上传")

1. 在“请求”部分中，执行以下步骤：

    ![请求](./media/airwatch-tutorial/ic791925.png "请求")  

    a. 对于“请求绑定类型”，请选择“POST”。

    b. 在 Azure 门户的“配置 AirWatch 的单一登录”对话框上，复制“登录 URL”值，然后将其粘贴到“标识提供程序单一登录 URL”文本框。

    c. 对于“NameID 格式”，请选择“电子邮件地址”。

    d. 对于“身份验证请求安全性”，请选择“无”。

    e. 单击“ **保存**”。

1. 再次单击“用户”选项卡。

    ![用户](./media/airwatch-tutorial/ic791926.png "用户")

1. 在“属性”部分中，执行以下步骤：

    ![属性](./media/airwatch-tutorial/ic791927.png "属性")

    a. 在“对象标识符”文本框中，键入 `http://schemas.microsoft.com/identity/claims/objectidentifier`。

    b. 在“用户名”文本框中，键入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。

    c. 在“显示名称”文本框中，键入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`。

    d. 在“名字”文本框中，键入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`。

    e. 在“姓氏”文本框中，键入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`。

    f. 在“电子邮件”文本框中，键入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。

    g. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 AirWatch 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。
1. 在应用程序列表中，选择“AirWatch”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-airwatch-test-user"></a>创建 AirWatch 测试用户

要使 Azure AD 用户能够登录 AirWatch，必须对其进行预配才能使其登录 AirWatch。 就 AirWatch 来说，预配任务需要手动完成。

**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录 **AirWatch** 公司站点。

2. 在左侧导航窗格中，单击“帐户”，并单击“用户”。
  
   ![用户](./media/airwatch-tutorial/ic791929.png "用户")

3. 在“用户”菜单中，单击“列表视图”，然后单击“添加”>“添加用户”。
  
   ![添加用户](./media/airwatch-tutorial/ic791930.png "添加用户")

4. 在“添加 / 编辑用户”对话框中，执行以下步骤：

   ![添加用户](./media/airwatch-tutorial/ic791931.png "添加用户")

   a. 键入希望在相关文本框中预配的有效 Azure Active Directory 帐户的“用户名”、“密码”、“确认密码”、“名字”、“姓氏”、“电子邮件地址”。

   b. 单击“ **保存**”。

> [!NOTE]
> 可使用其他任何 AirWatch 用户帐户创建工具或 AirWatch 提供的 API 预配 AAD 用户帐户。

### <a name="test-sso"></a>测试 SSO

选择访问面板中的 AirWatch 磁贴时，应会自动登录到为其设置了 SSO 的 AirWatch。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

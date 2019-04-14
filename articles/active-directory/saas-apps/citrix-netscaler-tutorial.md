---
title: 教程：Azure Active Directory 与 Citrix Netscaler 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Citrix Netscaler 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 6d434295a6a46ee5b7089608cbf788ff91589fb7
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281669"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-netscaler"></a>教程：Azure Active Directory 与 Citrix Netscaler 的集成

本教程介绍如何将 Citrix Netscaler 与 Azure Active Directory (Azure AD) 集成。
将 Citrix Netscaler 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 Citrix Netscaler。
* 可让用户使用其 Azure AD 帐户自动登录到 Citrix Netscaler（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Citrix Netscaler 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Citrix Netscaler 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Citrix Netscaler 支持 SP 发起的 SSO

* Citrix Netscaler 支持实时用户预配

## <a name="adding-citrix-netscaler-from-the-gallery"></a>从库添加 Citrix Netscaler

要配置 Citrix Netscaler 与 Azure AD 的集成，需要从库中将 Citrix Netscaler 添加到托管 SaaS 应用列表。

**若要从库中添加 Citrix Netscaler，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“Citrix Netscaler”，在结果面板中选择“Citrix Netscaler”，并单击“添加”按钮添加该应用程序。

     ![结果列表中的 Citrix Netscaler](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为 Britta Simon 的测试用户配置和测试 Citrix Netscaler 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Citrix Netscaler 中的相关用户之间建立链接关系。

若要配置并测试 Citrix Netscaler 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Citrix Netscaler 单一登录](#configure-citrix-netscaler-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Citrix Netscaler 测试用户](#create-citrix-netscaler-test-user)** - 在 Citrix Netscaler 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Citrix Netscaler 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)的“Citrix Netscaler”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![Citrix Netscaler 域和 URL 单一登录信息](common/sp-identifier-reply.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL： `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL： `https://<<Your FQDN>>`

    c. 在“回复 URL (断言使用者服务 URL)”文本框中，使用以下模式键入 URL： `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Citrix Netscaler 客户端支持团队](https://www.citrix.com/contact/technical-support.html)来获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

    > [!NOTE]
    > 要让 SSO 正常运行，这些 URL 应可通过公共网站进行访问。 需要在 Netscaler 端启用防火墙或其他安全设置，才能让 Azure AD 在所配置的 ACS URL 上发布令牌。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Citrix Netscaler”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-citrix-netscaler-single-sign-on"></a>配置 Citrix Netscaler 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Citrix Netscaler 租户。

2. 确保 **NetScaler 固件版本为 NS12.1:Build 48.13.nc**。

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure01.png)

3. 在“VPN 虚拟机”页上，执行以下步骤：

     ![配置单一登录](./media/citrix-netscaler-tutorial/configure02.png)

    a. 将“仅限 ICA”这一网关设置设为 true。
    
    b. 将“启用身份验证”设为 true。
    
    c. DTLS 是可选的。
    
    d. 请确保 SSLv3 设为“已禁用”。

4. 已创建自定义的“SSL 加密”组以在 https://www.ssllabs.com 上获取 A+，如下所示：

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure03.png)

5. 在“配置身份验证 SAML 服务器”页面上，执行以下步骤：

      ![配置单一登录](./media/citrix-netscaler-tutorial/configure04.png)

    a. 在“名称”文本框中，键入服务器的名称。

    b. 在“重定向 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    c. 在“单一注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    d. 在“IDP 证书名称”中，单击“+”符号以添加从 Azure 门户下载的证书。 上传后，请从下拉列表中选择证书。

    e. 还需要在此页面上设置以下字段

      ![配置单一登录](./media/citrix-netscaler-tutorial/configure24.png)

    f. 在“请求的身份验证上下文”中选择 Exact。

    g. 在“签名算法”中选择 RSA-SHA256。

    h. 在“摘要方法”中选择 SHA256。

    i. 勾选“强制实施用户名”。

    j. 单击 **“确定”**

6. 要配置“会话配置文件”，请执行以下步骤：

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure06.png)

    a. 在“名称”文本框中，键入会话配置文件的名称。

    b. 在“客户端体验”选项卡上，进行下述屏幕截图中所示的更改。

    c. 继续在“常规”选项卡上进行如下所示的更改，然后单击“确定”

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure07.png)

    d. 在“发布的应用程序”选项卡上，进行下述屏幕截图中所示的更改，然后单击“确定”。

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure08.png)

    e. 在“安全性”选项卡上，进行下述屏幕截图中所示的更改，然后单击“确定”。

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure09.png)

7. 在会话可靠性端口 2598 上建立 ICA 连接，如以下屏幕截图所示。

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure10.png)

8. 在“SAML”部分添加“服务器”，如以下屏幕截图所示 。

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure11.png)

9. 在“SAML”部分添加“策略”，如以下屏幕截图所示 。

     ![配置单一登录](./media/citrix-netscaler-tutorial/configure12.png)

10. 在“全局设置”页面上，转到“无客户端访问”部分。

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure13.png)

11. 在“配置”选项卡中执行以下步骤：

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure14.png)

    a. 选择“允许域”。

    b. 在“域名”文本框中，选择域。

    c. 单击“确定”。

12. 在“网站接收器”上进行店面设置，如以下屏幕截图所示：

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure15.png)

13. 在“管理身份验证方法 - 公司”部分执行以下步骤：

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure16.png)

    a. 选择“用户名和密码”。

    b. 选择“从 NetScaler 网关直通”。

    c. 单击“确定”。

14. 在“配置受信任的域”弹出窗口中，执行以下步骤：

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure17.png)

    a. 选择“仅限受信任的域”。

    b. 单击“添加”，以在“受信任的域”文本框中添加你的渔域。

    c. 从“默认域”列表中选择默认域。

    d. 选择“在登录页面显示域列表”。

    e. 单击“确定”。

15. 在“管理 NetScaler 网关”弹出窗口中，执行以下步骤：

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure18.png)

    a. 单击“添加”，以在“NetScaler 网关”文本框中添加 NetScaler 网关。

    b. 单击“关闭”。

16. 在“店面常规设置”选项卡上，执行以下步骤：

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure19.png)

    a. 在“显示名”文本框中，键入 NetScaler 网关名称。

    b. 在“NetScaler 网关 URL”文本框中，键入 NetScaler 网关 URL。

    c. 在“使用情况或角色”中选择“身份验证和 HDX 路由”。

    d. 单击“确定”。

17. 在“店面安全票证颁发机构”选项卡上，执行以下步骤：

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure20.png)

    a. 单击“添加”按钮，以在文本框中添加“安全票证颁发机构 URL”。

    b. 选择“启用会话可靠性”。

    c. 单击“确定”。

18. 在“店面身份验证设置”选项卡上，执行以下步骤：

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure21.png)

    a. 选择你的版本。

    b. 在“登录类型”中选择“域”。

    c. 输入你的回调 URL。

    d. 单击“确定”。

19. 在“店面部署 Citrix 接收器”选项卡上，执行以下步骤：

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure22.png)

    a. 在“部署选项”中选择“如果本地接收器不可用，在使用面向 HTML5 的接收器。

    b. 单击“确定”。

20. 在“管理信标”弹出窗口中，执行以下步骤：

    ![配置单一登录](./media/citrix-netscaler-tutorial/configure23.png)

    a. 在“内部信标”中选择“使用服务 URL”。

    b. 单击“添加”，以在“外部信标”文本框中添加你的 URL。

    c. 单击“确定”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过向 Britta Simon 授予 Citrix Netscaler 的访问权限使之能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Citrix Netscaler”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Citrix Netscaler”。

    ![应用程序列表中的 Citrix Netscaler 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-citrix-netscaler-test-user"></a>创建 Citrix Netscaler 测试用户

在本部分中，将在 Citrix Netscaler中创建一个名为 Britta Simon 的用户。 Citrix Netscaler 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Citrix Netscaler 中不存在用户，则会在身份验证后创建一个新用户。

>[!NOTE]
>如果需要手动创建用户，则需要联系 [Citrix Netscaler 客户端支持团队](https://www.citrix.com/contact/technical-support.html)。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Citrix Netscaler 磁贴时，应会自动登录到设置了 SSO 的 Citrix Netscaler。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory 中的条件访问是什么？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 教程：Azure Active Directory 与 NetSuite 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 NetSuite 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.openlocfilehash: 05e565d58faefbfc80815635afa38595177ad353
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807459"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>教程：Azure Active Directory 与 NetSuite 集成

本教程介绍如何将 NetSuite 与 Azure Active Directory (Azure AD) 集成。
将 NetSuite 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 NetSuite。
* 可以让用户使用其 Azure AD 帐户自动登录到 NetSuite（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 NetSuite 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 NetSuite 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* NetSuite 支持 IDP 发起的 SSO
* NetSuite 支持实时用户预配
* NetSuite 支持[自动用户预配](NetSuite-provisioning-tutorial.md)

## <a name="adding-netsuite-from-the-gallery"></a>从库中添加 NetSuite

若要配置 NetSuite 与 Azure AD 的集成，需要从库中将 NetSuite 添加到托管 SaaS 应用列表。

若要从库中添加 NetSuite，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“NetSuite”，在结果面板中选择“NetSuite”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 NetSuite](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户使用 NetSuite 配置和测试 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 NetSuite 相关用户之间建立链接关系。

若要配置和测试 NetSuite 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 NetSuite 单一登录](#configure-netsuite-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 NetSuite 测试用户](#create-netsuite-test-user)** - 在 NetSuite 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 NetSuite 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“NetSuite”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在“选择单一登录方法”对话框中，选择 SAML/WS-Fed 模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![NetSuite 域和 URL 单一登录信息](common/idp-reply.png)

    在“回复 URL”文本框中，使用以下模式键入 URL：

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`

    > [!NOTE]
    > 此值不是真实值。 请使用实际回复 URL 更新此值。 请联系 [NetSuite 客户端支持团队](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml)，获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

5. NetSuite 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性”对话框。

    ![图像](common/edit-attribute.png)

6. 在“用户属性”对话框的“用户声明”部分中，通过使用“编辑图标”编辑声明或使用“添加新声明”添加声明，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    | 名称 | 源属性 | 
    | ---------------| --------------- |
    | 帐户  | `account id` |

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 将“命名空间”留空。

    d. 选择“源”作为“属性”。

    e. 在“源属性”列表中，键入为该行显示的属性值。

    f. 单击“确定”

    g. 单击“ **保存**”。

    >[!NOTE]
    >帐户属性的值不是实际值。 将更新此值（本教程稍后将会介绍）。

4. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的元数据 XML 并将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 NetSuite”部分中，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-netsuite-single-sign-on"></a>配置 NetSuite 单一登录

1. 在浏览器中打开新标签页，并以管理员身分登录 NetSuite 公司站点。

2. 在页面顶部的工具栏中，单击“设置”，然后导航至“公司”，并单击“启用功能” **** ****  ****。

    ![配置单一登录](./media/NetSuite-tutorial/ns-setupsaml.png)

3. 在页面中间的工具栏中，单击“SuiteCloud” ****。

    ![配置单一登录](./media/NetSuite-tutorial/ns-suitecloud.png)

4. 在“管理身份验证”部分下，选择“SAML 单一登录”以在 NetSuite 中启用“SAML 单一登录”选项 ****  **** 。

    ![配置单一登录](./media/NetSuite-tutorial/ns-ticksaml.png)

5. 在页面顶部的工具栏中，单击“设置” ****。

    ![配置单一登录](./media/NetSuite-tutorial/ns-setup.png)

6. 从“设置任务”列表中，单击“集成”。

    ![配置单一登录](./media/NetSuite-tutorial/ns-integration.png)

7. 在“管理身份验证”部分中，单击“SAML 单一登录”。

    ![配置单一登录](./media/NetSuite-tutorial/ns-saml.png)

8. 在“SAML 设置”页的“NetSuite 配置”部分下，执行以下步骤：

    ![配置单一登录](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 选择“主要身份验证方法”。

    b. 对于标有“SAMLV2 标识提供者元数据”的字段，请选择“上传 IDP 元数据文件”。 然后单击“浏览”，上传从 Azure 门户下载的元数据文件。

    c. 单击“提交”。

9. 在 NetSuite 中，单击“设置”，然后导航至“公司”，并单击顶部导航菜单中的“公司信息”。

    ![配置单一登录](./media/NetSuite-tutorial/ns-com.png)

    ![配置单一登录](./media/NetSuite-tutorial/ns-account-id.png)

    b. 在右侧列的“公司信息”页中，复制**帐户 ID**。

    c. 将已从 NetSuite 帐户复制的**帐户 ID** 粘贴到 Azure AD 中的“属性值”字段中。 

10. 在用户能够单一登录到 NetSuite 之前，必须先在 NetSuite 中为他们分配适当的权限。 请按以下说明来分配这些权限。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在顶部导航菜单中单击“设置”。

    ![配置单一登录](./media/NetSuite-tutorial/ns-setup.png)

    b. 在左侧导航菜单中，选择“用户/角色”，并单击“管理角色”。

    ![配置单一登录](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. 单击“新建角色”。

    d. 键入新角色的“名称”。

    ![配置单一登录](./media/NetSuite-tutorial/ns-new-role.png)

    e. 单击“ **保存**”。

    f. 在顶部菜单中，单击“权限”。 然后单击“设置”。

    ![配置单一登录](./media/NetSuite-tutorial/ns-sso.png)

    g. 选择“SAML 单一登录”，并单击“添加”。

    h. 单击“ **保存**”。

    i. 在顶部导航菜单中单击“设置”，并单击“设置管理员”。

    ![配置单一登录](./media/NetSuite-tutorial/ns-setup.png)

    j. 在左侧导航菜单中，选择“用户/角色”，并单击“管理用户”。

    ![配置单一登录](./media/NetSuite-tutorial/ns-manage-users.png)

    k. 选择一个测试用户。 单击“编辑”，然后导航到“访问权限”选项卡。

    ![配置单一登录](./media/NetSuite-tutorial/ns-edit-user.png)

    l. 在“角色”对话框中，指定已创建的相应角色。

    ![配置单一登录](./media/NetSuite-tutorial/ns-add-role.png)

    m. 单击“ **保存**”。

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

在本部分中，通过授予 Britta Simon 访问 NetSuite 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“NetSuite”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“NetSuite”。

    ![应用程序列表中的 NetSuite 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-netsuite-test-user"></a>创建 NetSuite 测试用户

本部分将在 NetSuite 中创建一个名为 Britta Simon 的用户。 NetSuite 支持在默认情况下保持启用的实时用户预配。 此部分不存在任何操作项。 如果 NetSuite 中不存在用户，则会在身份验证后创建一个新用户。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 NetSuite 磁贴时，应当会自动登录到为其设置了 SSO 的 NetSuite。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [配置用户预配](NetSuite-provisioning-tutorial.md)


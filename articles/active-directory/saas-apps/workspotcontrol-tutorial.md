---
title: 教程：Azure Active Directory 与 Workspot Control 集成 | Microsoft Docs
description: 了解如何为 Azure Active Directory 和 Workspot Control 配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 086ec95531b01477be56d4b1a19d189f167a020f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086678"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>教程：Azure Active Directory 与 Workspot Control 集成

在本教程中，了解如何将 Workspot Control 与 Azure Active Directory (Azure AD) 集成。 将 Workspot Control 与 Azure AD 集成后，可以：

* 使用 Azure AD 控制有权访问 Workspot Control 的用户。
* 使用户可以使用其 Azure AD 帐户自动登录 Workspot Control（单一登录 [SSO]）。
* 在一个中心位置（Azure 门户）管理帐户。

有关 SaaS 应用程序与 Azure AD 集成的详细信息，请参阅[单一登录到 Azure AD 中的应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Workspot Control 的集成，需要以下项目：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

* 启用了 Workspot Control 单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

> [!Note]
> Workspot Control 支持 SP 和 IDP 发起的 SSO。


## <a name="adding-workspot-control-from-the-gallery"></a>从库中添加 Workspot Control

要配置 Workspot Control 与 Azure AD 的集成，必须从库中将 Workspot Control 添加到托管 SaaS 应用列表。

**若要从库中添加 Workspot Control，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”。 

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。  

    ![“企业应用程序”窗格](common/enterprise-applications.png)

3. 在窗口顶部选择“新建应用程序”  。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入“Workspot Control”  ，从结果面板中选择“Workspot Control”  ，然后选择“添加”  。

     ![“从库中添加”窗口](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，为测试用户 Britta Simon 配置和测试 Workspot Control 的 Azure AD 单一登录。
若要正常使用单一登录，必须在 Azure AD 用户与 Workspot Control 相关用户之间建立链接。

若要配置和测试 Workspot Control 的 Azure AD 单一登录，必须完成以下任务：

1. [配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)，使用户能够使用此功能。
2. [配置 Workspot Control 单一登录](#configure-workspot-control-single-sign-on)以在应用程序端配置单一登录设置。
3. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，为 Britta Simon 测试 Azure AD 单一登录。
4. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 Workspot Control 测试用户](#create-a-workspot-control-test-user)，在 Workspot Control 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. [测试单一登录](#test-single-sign-on)，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Workspot Control 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Workspot Control”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在“选择单一登录方法”窗口中，选择“SAML”模式以启用单一登录   。

    ![“选择单一登录选择方法”窗口](common/select-saml-option.png)

3. 在“设置 SAML 单一登录”页上，选择“编辑”（铅笔）图标以访问“基本 SAML 配置”。   

    ![“基本 SAML 配置”中突出显示的“编辑”图标](common/edit-urls.png)

4. 若要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置部分”中执行以下步骤： 

    ![Workspot Control 域和 URL 单一登录信息](common/idp-intiated.png)

    1. 在“标识符”  文本框中，输入采用以下模式的 URL：<br/>
    https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/metadata

    1. 在“回复 URL”  文本框中，输入采用以下模式的 URL：<br/>
    https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/assertion

5. 如果要在 SP 发起的模式下配置应用程序，选择“设置其他 URL”  。

    ![Workspot Control 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”  文本框中，输入采用以下模式的 URL：<br/>
    https://<<i></i>INSTANCENAME>-saml.workspot.com/

    > [!NOTE]
    > 这些不是实际值。 请使用实际的标识符、回复 URL 和登录 URL 替换这些值。 请联系 [Workspot Control 客户端支持团队](mailto:support@workspot.com)来获取这些值。 也可以参考 Azure 门户的“基本 SAML 配置”部分中显示的模式。 

6. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分，选择“下载”以根据要求从可用选项中下载“证书(Base64)”。     然后将其保存到计算机上。

    ![证书 (Base64) 下载链接](common/certificatebase64.png)

7. 在“设置 Workspot Control”部分中，根据要求复制响应的 URL  ：

    ![复制配置 URL](common/copy-configuration-urls.png)

    - **登录 URL**

    - **Azure AD 标识符**

    - **注销 URL**

### <a name="configure-workspot-control-single-sign-on"></a>配置 Workspot Control 单一登录

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Workspot Control。

2. 在页面顶部的工具栏中，选择“设置”。  ，然后选择“SAML”  。

    ![安装选项](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. 在“安全断言标记语言配置”窗口中，执行以下步骤  ：
 
    ![“安全断言标记语言配置”窗口](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. 在“实体 ID”框中，粘贴从 Azure 门户复制的“Azure AD 标识符”   。

    1. 在“登录服务 URL”框中，粘贴从 Azure 门户复制的“登录 URL”   。

    1. 在“注销服务 URL”框中，粘贴从 Azure 门户复制的“登录 URL”   。

    1. 选择“更新文件”，将从 Azure 门户下载的 base-64 编码证书上传到 X.509 证书中  。

    1. 选择“保存”。 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，将在 Azure 门户中创建一个测试用户。

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择窗口顶部的“新建用户”。 

    ![“新建用户”按钮](common/new-user.png)

3. 在用户属性中，执行以下步骤：

    ![用户属性窗口](common/user-properties.png)

    1. 在“姓名”  字段中，输入“BrittaSimon”  。
  
    1. 在“用户名”  字段中，输入“brittasimon@yourcompanydomain.extension”*  ***。 例如，输入“BrittaSimon@contoso<i></i>com”。

    1. 选中“显示密码”复选框  。 然后记下“密码”框中显示的值  。

    1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，授予 Britta Simon 访问 Workspot Control 的权限，以支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Workspot Control”。   

    ![“企业应用程序”窗格](common/enterprise-applications.png)

2. 从应用程序列表中，选择“Workspot Control”  。

    ![应用程序列表中的“Workspot Control”链接](common/all-applications.png)

3. 从左侧菜单中选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 选择“添加用户”按钮。  然后，在“添加分配”  窗口中选择“用户和组”  。

    ![“添加分配”窗口](common/add-assign-user.png)

5. 在“用户和组”窗口中，从“用户”列表选择“Britta Simon”。    然后单击“选择”  。

6. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”窗口中，从列表中为用户选择相应的角色。  然后单击底部的“选择”。 

7. 在“添加分配”  窗口中，选择“分配”  。

### <a name="create-a-workspot-control-test-user"></a>创建 Workspot Control 测试用户

若要使 Azure AD 用户能够登录 Workspot Control，必须将他们预配到 Workspot Control 中。 预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录 Workspot Control。

2. 在页面顶部的工具栏中，选择“用户”。  ，然后选择“添加用户”  。

    ![“用户”选项](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. 在“添加新用户”  窗口中，执行以下步骤：

    ![“添加新用户”窗口](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. 在“名字”框中，输入用户的名字，例如 Britta   。

    1. 在“姓氏”框中，输入用户的姓氏，例如 Simon   。

    1. 在“电子邮件”框中，输入用户的电子邮件地址，如 Brittasimon@contoso.<i></i>com。

    1. 从“角色”下拉列表中选择相应的用户角色  。

    1. 从“组”下拉列表中选择相应的用户组  。

    1. 选择“添加用户”。 

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，通过访问面板  测试 Azure AD 单一登录配置。

单击访问面板中的“Workspot Control”  磁贴时，应当会自动登录到为其设置了 SSO 的 Workspot Control。 有关详细信息，请参阅[访问面板简介](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)。

## <a name="additional-resources"></a>其他资源

- [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

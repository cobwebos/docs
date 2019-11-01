---
title: 教程：Azure Active Directory 与 HighGear 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 HighGear 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 55dcd2fb-96b7-46ec-9e69-eee71c535773
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed06586435315935566ca0b1519b182d4fc47d39
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159037"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>教程：Azure Active Directory 与 HighGear 的集成

本教程介绍如何将 HighGear 与 Azure Active Directory (Azure AD) 集成。
将 HighGear 与 Azure AD 集成可提供以下优势：

* 可在 Azure AD 中控制谁有权访问 HighGear。
* 可让用户使用其 Azure AD 帐户自动登录到 HighGear（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 HighGear 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 具有“企业”或“无限制”许可证的 HighGear 系统

## <a name="scenario-description"></a>方案描述

本教程介绍如何在测试环境中配置和测试 Azure AD 单一登录。

* HighGear 支持 **SP 和 IdP** 发起的 SSO

## <a name="adding-highgear-from-the-gallery"></a>从库中添加 HighGear

若要配置 HighGear 与 Azure AD 的集成，需要从库中将 HighGear 添加到托管 SaaS 应用列表。

**若要从库中添加 HighGear，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。 

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **HighGear**，在结果面板中选择“HighGear”，然后单击“添加”按钮添加该应用程序。  

     ![结果列表中的 HighGear](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分介绍如何基于名为 **Britta Simon** 的测试用户，配置并测试 HighGear 系统的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 HighGear 系统中的相关用户之间建立链接关系。

若要配置并测试 HighGear 系统的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 HighGear 单一登录](#configure-highgear-single-sign-on)** - 在 HighGear 应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 HighGear 测试用户](#create-highgear-test-user)** - 在 HighGear 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。 
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分介绍如何在 Azure 门户中启用 Azure AD 单一登录。

若要配置 HighGear 系统的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“HighGear”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标打开“基本 SAML 配置”对话框。   

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![HighGear 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中，粘贴 HighGear 系统中“单一登录设置”页上的“服务提供商实体 ID”字段值。  

    ![“服务提供商实体 ID”字段](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > 需要登录到 HighGear 系统才能访问“单一登录设置”页。 登录后，将鼠标移到 HighGear 中的“管理”选项卡上，然后单击“单一登录设置”菜单项。
    
    ![“单一登录设置”菜单项](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. 在“回复 URL”文本框中，粘贴 HighGear 系统中“单一登录设置”页上的“断言使用者服务(ACS) URL”值。  

    ![“断言使用者服务(ACS) URL”字段](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

     ![HighGear 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

     在“登录 URL”文本框中，粘贴 HighGear 系统中“单一登录设置”页上的“服务提供商实体 ID”字段值。   （此实体 ID 也是 HighGear 系统的基 URL，用于 SP 发起的单一登录。）

    ![“服务提供商实体 ID”字段](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > 这些不是实际值。 请这些值更新为 HighGear 系统中“单一登录设置”页上的实际“标识符”、“回复 URL”和“登录 URL”值。  如需帮助，请联系 [HighGear 支持团队](mailto:support@highgear.com)。

4. 在“使用 SAML 设置单一登录”  页的“SAML 签名证书”  部分中，单击“下载”  以下载“证书 (Base64)”  并将其保存在计算机上。 在稍后的单一登录配置步骤中，需要用到此证书。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 HighGear”部分，记下以下 URL 的位置。 

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL。 在执行下述“配置 HighGear 单一登录”的步骤 2 时，需要用到此值。 

    b. Azure AD 标识符。 在执行下述“配置 HighGear 单一登录”的步骤 3 时，需要用到此值。 

    c. 注销 URL。 在执行下述“配置 HighGear 单一登录”的步骤 4 时，需要用到此值。 

### <a name="configure-highgear-single-sign-on"></a>配置 HighGear 单一登录

若要配置 HighGear 单一登录，请登录到 HighGear 系统。 登录后，将鼠标移到 HighGear 中的“管理”选项卡上，然后单击“单一登录设置”菜单项。

![“单一登录设置”菜单项](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. 在“标识提供者名称”中，键入在“登录”页上单击“HighGear 单一登录”按钮时要显示的简短说明。  例如：Azure AD

2. 在 HighGear 中的“单一登录(SSO) URL”字段内，粘贴 Azure 中的“设置 HighGear”部分显示的“登录 URL”字段值。   

3. 在 HighGear 中的“标识提供者实体 ID”字段内，粘贴 Azure 中的“设置 HighGear”部分显示的“Azure AD 标识符”字段值。   

4. 在 HighGear 中的“单一注销(SLO) URL”字段内，粘贴 Azure 中的“设置 HighGear”部分显示的“注销 URL”字段值。   

5. 使用记事本打开从 Azure 中的“SAML 签名证书”部分下载的证书。  应已下载“证书(Base64)”格式的证书。  将记事本中的证书内容复制并粘贴到 HighGear 中的“标识提供者证书”字段。 

6. 向 [HighGear 支持团队](mailto:support@highgear.com)发送电子邮件以请求 HighGear 证书。 遵照该团队的说明填写“HighGear 证书”和“HighGear 证书密码”字段。  

7. 单击“保存”按钮保存 HighGear 单一登录配置。 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension    
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。 

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们向 Britta Simon 授予 HighGear 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“HighGear”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在“应用程序”列表中，选择“HighGear”。 

    ![“应用程序”列表中的“HighGear”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-highgear-test-user"></a>创建 HighGear 测试用户

若要创建 HighGear 测试用户用于测试单一登录配置，请登录到 HighGear 系统。

1. 单击“创建新联系人”按钮。 

    ![“创建新联系人”按钮](media/highgear-tutorial/create-new-contact-button.png)

    此时会显示一个菜单，可在其中选择要创建的联系人类型。

2. 单击“个人”菜单项以创建 HighGear 用户。 

    右侧将滑出一个窗格，可在其中键入新用户的信息。  
    ![“新建联系人”窗体](media/highgear-tutorial/new-contact-form.png)

3. 在“姓名”字段中，键入联系人的姓名。  例如：Britta Simon

4. 单击“更多选项”菜单，然后选择“帐户信息”菜单项。  

    ![单击“帐户信息”菜单项](media/highgear-tutorial/account-info-menu-item.png)

5. 将“可以登录”字段设置为“是”。 

    “启用单一登录”字段也会自动设置为“是”。 

6. 在“单一登录用户 ID”字段中，键入该用户的 ID。  例如： BrittaSimon@contoso.com

    现在，“帐户信息”部分的外观应如下所示：  
    ![完成后的“帐户信息”部分](media/highgear-tutorial/finished-account-info-section.png)

7. 若要保存该联系人，请单击窗格底部的“保存”按钮。 

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“HighGear”磁贴时，应会自动登录到为其设置了 SSO 的 HighGear。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


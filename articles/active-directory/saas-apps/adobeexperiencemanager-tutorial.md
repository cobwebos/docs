---
title: 教程：Azure Active Directory 与 Adobe Experience Manager 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Adobe Experience Manager 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f39751f40b32c5da24e13d75d2607d7da0a57ad3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "73154110"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>教程：Azure Active Directory 与 Adobe Experience Manager 的集成

本教程介绍如何将 Adobe Experience Manager 与 Azure Active Directory (Azure AD) 集成。
将 Adobe Experience Manager 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Adobe Experience Manager。
* 可以让用户使用其 Azure AD 帐户自动登录到 Adobe Experience Manager（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Adobe Experience Manager 的集成，需准备好以下各项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了单一登录的 Adobe Experience Manager 订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Adobe Experience Manager 支持 **SP 和 IDP** 发起的 SSO

* Adobe Experience Manager 支持**实时**用户预配

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>从库中添加 Adobe Experience Manager

若要配置 Adobe Experience Manager 与 Azure AD 的集成，需要将库中的 Adobe Experience Manager 添加到托管 SaaS 应用列表。

**若要从库中添加 Adobe Experience Manager，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入 **Adobe Experience Manager**，在结果面板中选择“Adobe Experience Manager”，然后单击“添加”按钮添加该应用程序   。

     ![结果列表中的 Adobe Experience Manager](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为 **Britta Simon** 的测试用户的指示配置和测试 [应用程序名称] 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 [应用程序名称] 相关用户之间建立链接关系。

若要配置和测试 [应用程序名称] 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Adobe Experience Manager 单一登录](#configure-adobe-experience-manager-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Adobe Experience Manager 测试用户](#create-adobe-experience-manager-test-user)** - 在 Adobe Experience Manager 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 [应用程序名称] 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **Adobe Experience Manager** 应用程序集成页上，选择“单一登录”  。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    ![Adobe Experience Manager 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中，同样键入已在 AEM 服务器上定义的唯一值  。

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > 答复 URL 值不是真实值。 请将回复 URL 值更新为实际回复 URL。 若要获取此值，请联系 [Adobe Experience Manager 客户端支持团队](https://helpx.adobe.com/support/experience-manager.html)来获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![Adobe Experience Manager 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”文本框中，键入 Adobe Experience Manager 服务器 URL  。

6. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 Adobe Experience Manager”  部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-adobe-experience-manager-single-sign-on"></a>配置 Adobe Experience Manager 单一登录

1. 在另一个浏览器窗口中打开“Adobe Experience Manager”管理门户  。

2. 选择“设置” > “安全” > “用户”。

    ![配置单一登录“保存”按钮](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. 选择“管理员”或其他任何相关用户  。

    ![配置单一登录“保存”按钮](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. 选择“帐户设置” > “管理信任存储”。

    ![配置单一登录“保存”按钮](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. 在“从 CER 文件添加证书”中单击“选择证书文件”   。 浏览并选择已从 Azure 门户下载的证书文件。

    ![配置单一登录“保存”按钮](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. 证书随即已添加到信任存储。 请记下证书的别名。

    ![配置单一登录“保存”按钮](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. 在“用户”页上，选择“身份验证服务”   。

    ![配置单一登录“保存”按钮](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. 选择“帐户设置” > “创建/管理密钥存储”。 通过提供密码创建密钥存储。

    ![配置单一登录“保存”按钮](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. 返回管理屏幕。 然后选择“设置” > “操作” > “Web 控制台”。

    ![配置单一登录“保存”按钮](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    此时会打开配置页。

    ![配置单一登录“保存”按钮](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. 找到“Adobe Granite SAML 2.0 身份验证处理程序”  。 然后选择“添加”图标  。

    ![配置单一登录“保存”按钮](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. 在此页上执行以下操作。

    ![配置单一登录“保存”按钮](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. 在“路径”框中，输入 **/**。

    b. 在“IDP URL”框中，输入从 Azure 门户复制的“登录 URL”值   。

    c. 在“IDP 证书别名”框中，输入在信任存储中添加的“证书别名”值   。

    d. 在“安全提供的实体 ID”文本框中，输入已在 Azure 门户中配置的唯一“Azure AD 标识符”值   。

    e. 在“断言使用者服务 URL”框中，输入已在 Azure 门户中配置的“回复 URL”值   。

    f. 在“密钥存储的密码”框中，输入已在密钥存储中设置的“密码”   。

    g. 在“用户属性 ID”框中，输入“名称 ID”或与具体情况相关的其他用户 ID   。

    h. 选择“自动创建 CRX 用户”。 

    i. 在“注销 URL”框中，输入从 Azure 门户获取的“注销 URL”值   。

    j. 选择“保存”。 

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

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分的步骤通过向 Britta Simon 授予对 Adobe Experience Manager 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Adobe Experience Manager”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Adobe Experience Manager”  。

    ![应用程序列表中的 Adobe Experience Manager 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-adobe-experience-manager-test-user"></a>创建 Adobe Experience Manager 测试用户

本部分的步骤在 Adobe Experience Manager 中创建名为 Britta Simon 的用户。 如果已选择“自动创建 CRX 用户”选项，则在成功完成身份验证后，会自动创建这些用户  。

若要手动创建用户，请与 [Adobe Experience Manager 支持团队](https://helpx.adobe.com/support/experience-manager.html) 配合，在 Adobe Experience Manager 平台中添加用户。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Adobe Experience Manager 磁贴时，应当会自动登录到你为其设置了 SSO 的 Adobe Experience Manager。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

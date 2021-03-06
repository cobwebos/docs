---
title: 教程：Azure Active Directory 与 LinkedIn Elevate 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 LinkedIn Elevate 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b7cb8d6ab34a632e36ea2fd1c87005a038bc523
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823712"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>教程：Azure Active Directory 与 LinkedIn Elevate 集成

本教程介绍了如何将 LinkedIn Elevate 与 Azure Active Directory (Azure AD) 进行集成。
将 LinkedIn Elevate 与 Azure AD 集成可具有以下优势：

* 可以在 Azure AD 中控制谁有权访问 LinkedIn Elevate。
* 可让用户使用其 Azure AD 帐户自动登录到 LinkedIn Elevate（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 LinkedIn Elevate 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 LinkedIn Elevate 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* LinkedIn Elevate 支持 **SP 和 IDP** 发起的 SSO

* LinkedIn Elevate 支持**实时**用户预配

* LinkedIn Elevate 支持[**自动**用户预配](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>从库中添加 LinkedIn Elevate

要配置 LinkedIn Elevate 与 Azure AD 的集成，需要从库中将 LinkedIn Elevate 添加到托管 SaaS 应用列表。

**若要从库中添加 LinkedIn Elevate，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **LinkedIn Elevate**，在结果面板中选择“LinkedIn Elevate”，然后单击“添加”按钮添加该应用程序。  

    ![结果列表中的“LinkedIn Elevate”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 LinkedIn Elevate 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 LinkedIn Elevate 相关用户之间建立链接关系。

若要配置并测试 LinkedIn Elevate 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 LinkedIn Elevate 单一登录](#configure-linkedin-elevate-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 LinkedIn Elevate 测试用户](#create-linkedin-elevate-test-user)** - 在 LinkedIn Elevate 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 LinkedIn Elevate 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“LinkedIn Elevate”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    ![LinkedIn Elevate 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文字框中，输入“实体 ID”值。本教程稍后会介绍如何从 LinkedIn 门户复制“实体 ID”值。  

    b. 在“回复 URL”文字框中，输入“断言使用者访问(ACS) URL”值。本教程稍后会介绍如何从 LinkedIn 门户复制“断言使用者访问(ACS) URL”值。  

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    ![LinkedIn Elevate 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

6. LinkedIn Elevate 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。 LinkedIn Elevate 应用程序要求 nameidentifier 与 **user.mail** 相映射，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射。

    ![image](common/edit-attribute.png)

7. 除上述属性以外，LinkedIn Elevate 应用程序还要求在 SAML 响应中传回其他几个属性。 在“用户属性”对话框的“用户声明”部分执行以下步骤，以添加 SAML 令牌属性，如下表中所示： 

    | Name | 源属性|
    | -------| -------------|
    | department | user.department |

    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“确定” 

    g. 单击“ **保存**”。

8. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

9. 在“设置 LinkedIn Elevate”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-linkedin-elevate-single-sign-on"></a>配置 LinkedIn Elevate 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 LinkedIn Elevate 租户。

1. 在“帐户中心”  ，单击“设置”  下的“全局设置”  。 此外，请从下拉列表中选择“提升 - 提升 AAD 测试”  。

    ![配置单一登录](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. 单击“或单击此处从表单加载并复制单个字段”并执行以下步骤： 

    ![配置单一登录](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. 复制“实体 ID”，并将其粘贴到 Azure 门户上“基本 SAML 配置”中的“标识符”文本框内。   

    b. 复制“断言使用者访问(ACS) URL”，并将其粘贴到 Azure 门户上“基本 SAML 配置”中的“回复 URL”文本框内。   

1. 转到“LinkedIn 管理设置”  分区。 单击“上传 XML 文件”选项，上传从 Azure 门户下载的 XML 文件。

    ![配置单一登录](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. 单击“开启”  启用 SSO。 SSO 状态将从“未连接”  更改为“已连接” 

    ![配置单一登录](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中键入 `brittasimon@yourcompanydomain.extension`。  例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 LinkedIn Elevate 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“LinkedIn Elevate”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“LinkedIn Elevate”  。

    ![“应用程序”列表中的“LinkedIn Elevate”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-linkedin-elevate-test-user"></a>创建 LinkedIn Elevate 测试用户

LinkedIn Elevate 应用程序支持及时用户设置，进行身份验证后，会在应用程序中自动创建用户。 在 LinkedIn Elevate 门户的管理设置页上，将“自动分配许可证”  开关切换为活动状态及时预配，这也会将许可证分配给用户。 LinkedIn Elevate 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](linkedinelevate-provisioning-tutorial.md)。

   ![创建 Azure AD 测试用户](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“LinkedIn Elevate”磁贴时，应会自动登录到设置了 SSO 的 LinkedIn Elevate。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
---
title: 教程：Azure Active Directory 与 EBSCO 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 EBSCO 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 35cb408473da8c6397c5034ae20ac0a50b0953ea
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944723"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>教程：Azure Active Directory 与 EBSCO 集成

本教程介绍如何将 EBSCO 与 Azure Active Directory (Azure AD) 集成。
将 EBSCO 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 EBSCO
* 可让用户使用其 Azure AD 帐户自动登录到 EBSCO（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 EBSCO 的集成，需要以下项目：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 EBSCO 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* EBSCO 支持 **SP** 和 **IDP** 发起的 SSO

* EBSCO 支持**实时**用户预配

## <a name="adding-ebsco-from-the-gallery"></a>从库中添加 EBSCO

若要配置 EBSCO 与 Azure AD 的集成，需要从库中将 EBSCO 添加到托管 SaaS 应用列表。

**若要从库中添加 EBSCO，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮  。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **EBSCO**，在结果面板中选择“EBSCO”，然后单击“添加”按钮添加该应用程序。  

     ![结果列表中的 EBSCO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 EBSCO 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 EBSCO 相关用户之间建立链接关系。

若要配置和测试 EBSCO 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 EBSCO 单一登录](#configure-ebsco-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 EBSCO 测试用户](#create-ebsco-test-user)** - 在 EBSCO 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 EBSCO 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“EBSCO”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标打开“基本 SAML 配置”对话框。   

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置部分”中执行以下步骤   ：

    ![EBSCO 域和 URL 单一登录信息](common/idp-identifier.png)

    在“标识符”  文本框中，键入一个 URL：`pingsso.ebscohost.com`

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    ![image](common/both-preintegrated-signon.png)

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > 登录 URL 值不是实际值。 请使用实际登录 URL 更新此值。 请联系 [EBSCO 客户端支持团队](mailto:sso@ebsco.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

    o   **唯一元素：**  

    o   **Custid** = 输入唯一的 EBSCO 客户 ID 

    o   **Profile** = 客户端可以定制链接，以将用户定向到特定的配置文件（具体取决于从 EBSCO 购买的产品）。 可以输入特定的配置文件 ID。 主 ID 是 eds (EBSCO Discovery Service) 和 ehost（EBSOCOhost 数据库）。 [此处](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile)提供了相同操作的说明。

6. EBSCO 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标打开“用户属性”对话框。  

    ![image](common/edit-attribute.png)

     > [!Note]
    > **name** 属性是必需的，将映射到 EBSCO 应用程序中的“名称标识符值”。  默认已添加此属性，因此无需手动添加。

7. 除上述属性以外，EBSCO 应用程序还要求在 SAML 响应中传回其他几个属性。 在“用户属性”  对话框的“用户声明”  部分执行以下步骤，以便添加 SAML 令牌属性，如下表所示： 

    | Name | 源属性|
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | 电子邮件   | user.mail |

    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“ **保存**”。

8. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

9. 在“设置 EBSCO”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-ebsco-single-sign-on"></a>配置 EBSCO 单一登录

若要在 **EBSCO** 端配置单一登录，需要将下载的“元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [EBSCO 支持团队](mailto:sso@ebsco.com)。  他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension。  例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过向 Britta Simon 授予 EBSCO 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“EBSCO”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“EBSCO”  。

    ![“应用程序”列表中的“EBSCO”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-ebsco-test-user"></a>创建 EBSCO 测试用户

在 EBSCO 中，用户预配可自动完成。

**若要预配用户帐户，请执行以下步骤：**

Azure AD 将所需的数据传递给 EBSCO 应用程序。 EBSCO 的用户预配可以自动完成，或要求填写表单一次。 具体的过程取决于客户端是否包含大量保存了个人设置的现有 EBSCOhost 帐户。 在实施过程中，可与 [EBSCO 支持团队](mailto:sso@ebsco.com)讨论上述过程。 不管采用哪种方法，客户端都不需要在测试之前创建任何 EBSCOhost 帐户。

   >[!Note]
   >可以自动执行 EBSCOhost 用户预配/个性化。 有关实时用户预配过程，请联系 [EBSCO 支持团队](mailto:sso@ebsco.com)。 

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

1. 在访问面板中单击“EBSCO”磁贴时，应会自动登录到 EBSCO 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。

2. 登录到该应用程序后，单击右上角的“登录”按钮。 

    ![“应用程序”列表中的 EBSCO 登录按钮](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. 此时会出现一次性提示“现在请将现有的 MyEBSCOhost 帐户链接到机构帐户”或“创建新的 MyEBSCOhost 帐户并将其链接到机构帐户”，以便配对机构/SAML 登录名。   该帐户用于在 EBSCOhost 应用程序中进行个性化。 选择选项“创建新帐户”，将会看到，用于个性化的窗体中预先填充了 SAML 响应中的值，如以下屏幕截图所示。  单击“继续”保存所做的选择。 
    
     ![“应用程序”列表中的 EBSCO 用户](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. 完成上述设置后，清除 Cookie/缓存并再次登录。 这一次不再需要手动登录，因为系统已记住个性化设置

## <a name="additional-sesources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


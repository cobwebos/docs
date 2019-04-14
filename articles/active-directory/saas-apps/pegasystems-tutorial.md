---
title: 教程：Azure Active Directory 与 Pega Systems 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Pega Systems 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 34fe5d85829d909989513214406ba96ea5be0aa8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271095"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>教程：zure Active Directory 与 Pega Systems 集成

在本教程中，了解如何将 Pega Systems 与 Azure Active Directory (Azure AD) 集成。
将 Pega Systems 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Pega Systems。
* 可让用户使用其 Azure AD 帐户自动登录到 Pega Systems（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Pega Systems 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Pega Systems 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Pega Systems 支持 **SP** 和 **IDP** 发起的 SSO

## <a name="adding-pega-systems-from-the-gallery"></a>从库添加 Pega Systems

要配置 Pega Systems 与 Azure AD 的集成，需要从库中将 Pega Systems 添加到托管 SaaS 应用的列表。

**若要从库中添加 Pega Systems，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Pega Systems”，在结果面板中选择“Pega Systems”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 Pega Systems](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Pega Systems 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Pega Systems 相关用户之间建立链接关系。

若要配置并测试 Pega Systems 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Pega Systems 单一登录](#configure-pega-systems-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Pega Systems 测试用户](#create-pega-systems-test-user)** - 在 Pega Systems 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Pega Systems 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Pega Systems”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤：

    ![Pega Systems 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL： `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL： `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![Pega Systems 域和 URL 单一登录信息](common/both-advanced-urls.png)

    a. 在“登录 URL”文本框中，键入登录 URL 值。

    b. 在“中继状态”文本框中，使用以下模式键入 URL： `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > 这些不是实际值。 使用实际的“标识符”、“回复 URL”、“登录 URL”和“中继状态 URL”更新这些值。 你可以从 Pega 应用程序中找到标识符和回复 URL 的值，本教程稍后将介绍这些值。 请联系 [Pega Systems 客户端支持团队](https://www.pega.com/contact-us)获取“中继状态”值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

6. Pega Systems 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标打开“用户属性”对话框 ****  **** 。

    ![图像](common/edit-attribute.png)

7. 除上述属性以外，Pega Systems 应用程序还要求在 SAML 响应中传回其他几个属性。 在“用户属性”对话框的“用户声明”部分执行以下步骤，以便添加 SAML 令牌属性，如下表所示：

    | 名称 | 源属性|
    | ------------------- | -------------------- |
    | uid | *********** |
    | cn  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | organization | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | 工作组 | *********** |
    | 电话 | *********** |

    > [!NOTE]
    > 这些是客户特定的值。 请提供适当的值。

    a. 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 将“命名空间”留空。

    d. 选择“源”作为“属性”。

    e. 在“源属性”列表中，键入为该行显示的属性值。

    f. 单击“确定”

    g. 单击“ **保存**”。

8. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

9. 在“设置 Pega Systems”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-pega-systems-single-sign-on"></a>配置 Pega Systems 单一登录

1. 要在“Pega Systems”端上配置单一登录，请在另一个浏览器窗口中使用管理员帐户打开“Pega 门户”。

2. 选择“创建” -> “SysAdmin” -> “身份验证服务”。

    ![配置单一登录“保存”按钮](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. 在“创建身份验证服务”屏幕上执行以下操作：

    ![配置单一登录“保存”按钮](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. 从“类型”中选择“SAML 2.0”

    b. 在“名称”文本框中，输入任何名称，例如 Azure AD SSO

    c. 在“简短说明”文本框中，输入任何说明  

    d. 单击“创建并打开” 
    
4. 在“标识提供者 (IdP) 信息”部分中，单击“导入 IdP 元数据”，然后浏览已从 Azure 门户下载的元数据文件。 单击“提交”以加载元数据。

    ![配置单一登录“保存”按钮](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
5. 该操作将填充 IdP 数据，如下所示。

    ![配置单一登录“保存”按钮](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. 在“服务提供者 (SP) 设置”部分执行以下操作：

    ![配置单一登录“保存”按钮](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. 复制“实体标识”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”中的“标识符”文本框内。

    b. 复制“断言使用者服务(ACS)位置”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”中的“回复 URL”文本框内。

    c. 选择“禁用请求签名”。

7. 单击“保存”

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension。 例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问  Pega Systems 的权限，以支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Pega Systems”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Pega Systems”。

    ![应用程序列表中的 Pega Systems 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-pega-systems-test-user"></a>创建 Pega Systems 测试用户

本部分的目的是在 Pega Systems 中创建一个名为“Britta Simon”的用户。 与 [Pega Systems 支持团队](https://www.pega.com/contact-us)配合，在 Pega Systems 中创建用户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Pega Systems”磁贴时，应会自动登录到设置了 SSO 的 Pega Systems。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory 中的条件访问是什么？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
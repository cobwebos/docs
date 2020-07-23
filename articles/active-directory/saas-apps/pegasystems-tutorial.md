---
title: 教程：Azure Active Directory 与 Pega Systems 的集成 | Microsoft 文档
description: 本教程介绍如何在 Azure Active Directory 与 Pega Systems 之间配置单一登录。
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
ms.openlocfilehash: bd54466e054055ff84cd5bb2b28c5cc074ac0017
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026799"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>教程：Azure Active Directory 与 Pega Systems 集成

本教程介绍如何将 Pega Systems 与 Azure Active Directory (Azure AD) 集成。

这种集成可提供以下优势：

* 可以使用 Azure AD 控制谁有权访问 Pega Systems。
* 可让用户使用其 Azure AD 帐户自动登录到 Pega Systems（单一登录）。
* 可在一个中心位置（即 Azure 门户）管理帐户。

若要了解有关 SaaS 应用程序与 Azure AD 集成的详细信息，请参阅[单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

如果没有 Azure 订阅，可以在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Pega Systems 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以注册[一个月试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 已启用单一登录的 Pega Systems 订阅

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD 单一登录。

* Pega Systems 支持 SP 和 IdP 发起的 SSO。

## <a name="add-pega-systems-from-the-gallery"></a>从库中添加 Pega Systems

若要配置 Pega Systems 与 Azure AD 的集成，需要从库中将 Pega Systems 添加到托管 SaaS 应用列表。

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”： 

    ![选择“Azure Active Directory”](common/select-azuread.png)

2. 转到“企业应用程序”   > “所有应用程序”  。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加应用程序，请在窗口顶部选择“新建应用程序”  ：

    ![选择“新建应用程序”](common/add-new-app.png)

4. 在搜索框中输入 **Pega Systems**。 在搜索结果中选择“Pega Systems”，然后选择“添加”。  

     ![搜索结果](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们将使用名为 Britta Simon 的测试用户来配置并测试 Pega Systems 的 Azure AD 单一登录。
若要启用单一登录，需要在 Azure AD 用户与 Pega Systems 中的对应用户之间建立关系。

若要配置并测试 Pega Systems 的 Azure AD 单一登录，需要完成以下步骤：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** ，为用户启用该功能。
2. 在应用程序端 **[配置 Pega Systems 单一登录](#configure-pega-systems-single-sign-on)** 。
3. **创建 Azure AD 测试用户[，以测试 Azure AD 单一登录](#create-an-azure-ad-test-user)** 。
4. **分配 Azure AD 测试用户[，为用户启用 Azure AD 单一登录](#assign-the-azure-ad-test-user)** 。
5. 创建与用户的 Azure AD 表示形式相链接的 **[Pega Systems 测试用户](#create-a-pega-systems-test-user)** 。
6. **[测试单一登录](#test-single-sign-on)** ，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Pega Systems 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Pega Systems”应用程序集成页上，选择“单一登录”：  

    ![选择“单一登录”](common/select-sso.png)

2. 在“选择单一登录方法”对话框中，选择“SAML/WS-Fed”模式以启用单一登录：  

    ![选择单一登录方法](common/select-saml-option.png)

3. 在“设置 SAML 单一登录”页上，选择“编辑”图标打开“基本 SAML 配置”对话框：   

    ![“编辑”图标](common/edit-urls.png)

4. 若要在 IdP 发起的模式下配置应用程序，请在“基本 SAML 配置”对话框中完成以下步骤。 

    ![“基本 SAML 配置”对话框](common/idp-intiated.png)

    1. 在“标识符”框中，输入采用以下模式的 URL： 

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. 在“回复 URL”框中，输入采用以下模式的 URL： 

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. 若要在 SP 发起的模式下配置应用程序，请选择“设置其他 URL”并完成以下步骤。 

    ![Pega Systems 域和 URL 单一登录信息](common/both-advanced-urls.png)

    1. 在“登录 URL”框中，输入登录 URL 值。 

    1. 在“中继状态”框中，输入采用以下模式的 URL：`https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > 此处提供的值为占位符。 使用实际的标识符、回复 URL、登录 URL 和中继状态 URL。 可以根据本教程稍后所述，从 Pega 应用程序获取标识符和回复 URL 值。 若要获取中继状态值，请联系 [Pega Systems 支持团队](https://www.pega.com/contact-us)。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

6. Pega Systems 应用程序需要特定格式的 SAML 断言。 若要获取正确格式的 SAML 断言，需将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性。 选择“编辑”  图标以打开“用户属性”  对话框：

    ![用户属性](common/edit-attribute.png)

7. 除上面屏幕截图中所示的属性以外，Pega Systems 应用程序还要求在 SAML 响应中传回其他几个属性。 在“用户属性”对话框的“用户声明”部分，完成以下步骤以添加这些 SAML 令牌属性：  

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > 这些值特定于组织。 请提供适当的值。

    1. 选择“添加新声明”打开“管理用户声明”对话框：  

    ![选择“添加新声明”](common/new-save-attribute.png)

    ![“管理用户声明”对话框](common/new-attribute-details.png)

    1. 在“名称”框中，键入该行显示的属性名称。 

    1. 将“命名空间”框留空  。

    1. 对于“源”，请选择“属性”。  

    1. 在“源属性”列表中，选择为该行显示的属性值。 

    1. 选择“确定”  。

    1. 选择“保存”。 

8. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，根据要求选择“联合元数据 XML”旁边的“下载”链接，并将证书保存在计算机上：    

    ![证书下载链接](common/metadataxml.png)

9. 在“设置 Pega Systems”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

    1. **登录 URL**。

    1. **Azure AD 标识符**。

    1. **注销 URL**。

### <a name="configure-pega-systems-single-sign-on"></a>配置 Pega Systems 单一登录

1. 若要在 **Pega Systems** 端配置单一登录，请在另一个浏览器窗口中使用管理员帐户登录到 Pega 门户。

2. 选择“创建” > “SysAdmin” > “身份验证服务”：

    ![选择“身份验证服务”](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. 在“创建身份验证服务”屏幕上完成以下步骤。 

    ![“创建身份验证服务”屏幕](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. 在“类型”列表中，选择“SAML 2.0”。  

    1. 在“名称”框中输入任意名称（例如 **Azure AD SSO**）。

    1. 在“简短说明”框中输入说明。   

    1. 选择“创建并打开”。 
    
4. 在“标识提供者(IdP)信息”部分，选择“导入 IdP 元数据”，然后浏览到已从 Azure 门户下载的元数据文件。   单击“提交”以加载元数据： 

    ![“标识提供者(IdP)信息”部分](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    导入操作将填充 IdP 数据，如下所示：

    ![导入的 IdP 数据](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. 在“服务提供程序(SP)设置”部分完成以下步骤。 

    ![服务提供程序设置](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. 复制“实体标识”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符”框中。   

    1. 复制“断言使用者服务(ACS)位置”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”框中。   

    1. 选择  “禁用请求签名”。

7. 选择“保存”。 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”、“所有用户”：   

    ![选择“所有用户”](common/users.png)

2. 选择屏幕顶部的“新建用户”： 

    ![选择“新建用户”](common/new-user.png)

3. 在“用户”对话框中完成以下步骤。 

    ![“用户”对话框](common/user-properties.png)

    a. 在“姓名”  框中，输入 **BrittaSimon**。
  
    b. 在“用户名”框中，输入 **brittasimon@\<公司域>.\<扩展>**。 （例如：BrittaSimon@contoso.com。）

    c. 选择“显示密码”，然后记下“密码”框中的值   。

    d. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 Pega Systems 的权限，使她能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Pega Systems”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中选择“Pega Systems”。 

    ![应用程序列表](common/all-applications.png)

3. 在左窗格中选择“用户和组”： 

    ![选择“用户和组”](common/users-groups-blade.png)

4. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![选择“添加用户”](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”，然后单击屏幕底部的“选择”按钮。   

6. 如果希望在 SAML 断言中使用角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。  单击屏幕底部的“选择”按钮  。

7. 在“添加分配”对话框中选择“分配”。  

### <a name="create-a-pega-systems-test-user"></a>创建 Pega Systems 测试用户

接下来，需要在 Pega Systems 中创建一个名为 Britta Simon 的用户。 请在 [Pega Systems 支持团队](https://www.pega.com/contact-us)的配合下创建用户。

### <a name="test-single-sign-on"></a>测试单一登录

现在，需要使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择“Pega Systems”磁贴时，应会自动登录到设置了 SSO 的 Pega Systems 实例。 有关详细信息，请参阅[在“我的应用”门户中访问和使用应用](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他资源

- [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
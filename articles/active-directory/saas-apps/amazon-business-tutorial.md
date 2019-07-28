---
title: 教程：Azure Active Directory 与 Amazon Business 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Amazon Business 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b2597a66-afd9-4f11-b14b-646b597bb6c2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7ac085beaa85a7ddf3a6c3bfc61820e8e5a63ea
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68496562"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>教程：将 Amazon Business 与 Azure Active Directory 集成

本教程介绍如何将 Amazon Business 与 Azure Active Directory (Azure AD) 集成。 将 [Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Amazon Business。
* 让用户使用其 Azure AD 帐户自动登录到 Amazon Business。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以从[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月免费试用版。
* 已启用 Amazon Business 单一登录 (SSO) 的订阅。 转到 [Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) 页面创建 Amazon Business 帐户。

## <a name="scenario-description"></a>方案描述

在本教程中，你将在现有的 Amazon Business 帐户中配置并测试 Azure AD SSO。

* Amazon Business 支持 **SP 和 IDP** 发起的 SSO
* Amazon Business 支持**实时**用户预配

## <a name="adding-amazon-business-from-the-gallery"></a>从库中添加 Amazon Business

若要配置 Amazon Business 与 Azure AD 的集成，需要从库中将 Amazon Business 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Amazon Business**。 
1. 从结果面板中选择“Amazon Business”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Amazon Business 的 Azure AD SSO。

若要配置并测试 Amazon Business 的 Azure AD SSO，请完成以下构建步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. **[配置 Amazon Business SSO](#configure-amazon-business-sso)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
5. **[创建 Amazon Business 测试用户](#create-amazon-business-test-user)** - 在 Amazon Business 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Amazon Business”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 若要在“IDP”发起模式下进行配置，请在“基本 SAML 配置”部分执行以下步骤：  

    1. 在“标识符(实体 ID)”文本框中，使用以下模式之一键入 URL： 
    
       | | |
       |-|-|
       | `https://www.amazon.com`|
       | `https://www.amazon.co.jp`|
       | `https://www.amazon.de`|

    1. 在“回复 URL”文本框中，使用以下模式之一键入 URL： 
    
       | | |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`|

       > [!NOTE]
       > 答复 URL 值不是真实值。 请使用实际回复 URL 更新此值。 你将从 Amazon Business SSO 配置部分获取 `<idpid>` 值，本教程稍后会对此进行介绍。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：`https://www.amazon.com/` 

1. 以下屏幕截图显示了默认属性的列表。 在“用户属性和声明”部分单击“编辑”图标以编辑属性。  

    ![属性](media/amazon-business-tutorial/map-attribute3.png)

1. 编辑“属性”，并将这些属性的“命名空间”值复制到记事本中。 

    ![属性](media/amazon-business-tutorial/map-attribute4.png)

1. 除了上述属性以外，Amazon Business 应用程序还要求在 SAML 响应中传回其他几个属性。 在“组声明”对话框中的“用户属性和声明”部分执行以下步骤：  

    a. 单击“声明中返回的组”旁边的**笔**。 

    ![image](./media/amazon-business-tutorial/config04.png)

    ![图像](./media/amazon-business-tutorial/config05.png)

    b. 从单选列表中选择“所有组”。 

    c. 选择“组 ID”作为“源属性”。  

    d. 选中“自定义组声明的名称”复选框，并根据组织的要求输入组名称。 

    e. 单击“ **保存**”。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中找到“元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Amazon Business”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>配置 Amazon Business SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Amazon Business 公司站点。

1. 单击“用户配置文件”并选择“业务设置”。  

    ![用户配置文件](media/amazon-business-tutorial/user-profile.png)

1. 在“系统集成”向导中，选择“单一登录(SSO)”。  

    ![单一登录 (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. 在“设置 SSO”向导中，根据组织的要求选择提供程序，然后单击“下一步”。  

    ![默认组](media/amazon-business-tutorial/default-group1.png)

1. 在“新用户帐户的默认设置”向导中选择“默认组”，根据组织中的用户角色选择“默认购买角色”，然后单击“下一步”。    

    ![默认组](media/amazon-business-tutorial/dafault-group2.png)

1. 在“上传元数据文件”向导中，单击“浏览”以上传从 Azure 门户下载的“元数据 XML”文件，然后单击“上传”。    

    ![连接数据](media/amazon-business-tutorial/connection-data1.png)

1. 上传下载的元数据文件后，“连接数据”部分中的字段会自动填充。  然后单击“下一步”。 

    ![连接数据](media/amazon-business-tutorial/connection-data2.png)

1. 在“上传属性语句”向导中，单击“跳过”。  

    ![属性](media/amazon-business-tutorial/map-attribute1.png)

1. 在“属性映射”向导中，单击“+ 添加字段”选项添加要求字段。   将包含从 Azure 门户的“用户属性和声明”部分复制的命名空间的属性值添加到“SAML 属性名称”字段，然后单击“下一步”。   

    ![属性](media/amazon-business-tutorial/map-attribute2.png)

1. 在“Amazon 连接数据”向导中，单击“下一步”。  

    ![连接](media/amazon-business-tutorial/amazon-connect.png)

1. 请检查已配置的步骤的“状态”，然后单击“开始测试”。  

    ![连接](media/amazon-business-tutorial/sso-connection1.png)

1. 在“测试 SSO 连接”向导中，单击“测试”。  

    ![连接](media/amazon-business-tutorial/sso-connection2.png)

1. 在“IDP 发起的 URL”向导中单击“激活”之前，请复制分配给 **idpid** 的值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”中的 **idpid** 参数内。    

    ![连接](media/amazon-business-tutorial/sso-connection3.png)

1. 在“是否已准备好切换到活动的 SSO?”向导中，选中“我已全面测试 SSO 并已准备好激活”复选框，然后单击“切换到活动状态”。   

    ![连接](media/amazon-business-tutorial/sso-connection4.png)

1. 最后，在“SSO 连接详细信息”部分，“状态”将显示为“活动”。   

    ![连接](media/amazon-business-tutorial/sso-connection5.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

> [!NOTE]
> 管理员需要根据情况在其租户中创建测试用户。 以下步骤说明如何创建测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>在 Azure 门户中创建 Azure AD 安全组

1. 单击“Azure Active Directory”>“所有组”。 

    ![创建 Azure AD 安全组](./media/amazon-business-tutorial/all-groups-tab.png)

1. 单击“新建组”： 

    ![创建 Azure AD 安全组](./media/amazon-business-tutorial/new-group-tab.png)

1. 填写“组类型”、“组名称”、“组说明”和“成员资格类型”。     单击箭头选择成员，然后搜索或单击要添加到该组的成员。 单击“选择”以添加选定的成员，然后单击“创建”。  

    ![创建 Azure AD 安全组](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Amazon Business 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“Amazon Business”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

    >[!NOTE]
    > 如果未在 Azure AD 中分配用户，将收到以下错误。

    ![“添加用户”链接](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>在 Azure 门户中分配 Azure AD 安全组

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Amazon Business”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在“应用程序”列表中，键入并选择“Amazon Business”。 

    ![“应用程序”列表中的“Amazon Business”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”。 

    ![“添加分配”窗格](common/add-assign-user.png)

5. 搜索要使用的安全组，然后单击该组将其添加到“选择成员”部分。 依次单击“选择”、“分配”   。

    ![搜索安全组](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > 检查菜单栏中的通知，确定是否已在 Azure 门户中将该组成功分配到企业应用程序。

### <a name="create-amazon-business-test-user"></a>创建 Amazon Business 测试用户

在本部分，你将在 Amazon Business 中创建名为 B.Simon 的用户。 Amazon Business 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 Amazon Business 中尚不存在用户，身份验证后会创建一个新用户。

### <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Amazon Business”磁贴时，应会自动登录到设置了 SSO 的 Amazon Business。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

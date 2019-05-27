---
title: 教程：Azure Active Directory 与 Wdesk 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Wdesk 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 71feb455457fdf75fb19121bac1927b42fe38b67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "65865339"
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>教程：Azure Active Directory 与 Wdesk 集成

在本教程中，了解如何将 Wdesk 与 Azure Active Directory (Azure AD) 集成。
将 Wdesk 与 Azure AD 集成有以下优势：

* 可以在 Azure AD 中控制谁有权访问 Wdesk。
* 可让用户使用其 Azure AD 帐户自动登录到 Wdesk（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Wdesk 的集成，需要以下项目：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 Wdesk 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Wdesk 支持 **SP** 和 **IDP** 发起的 SSO

## <a name="adding-wdesk-from-the-gallery"></a>从库中添加 Wdesk

若要配置 Wdesk 与 Azure AD 的集成，需要从库中将 Wdesk 添加到托管 SaaS 应用列表。

若要从库中添加 Wdesk，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **Wdesk**，在结果面板中选择“Wdesk”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的“Wdesk”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Wdesk 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Wdesk 相关用户之间建立链接关系。

若要配置和测试 Wdesk 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Wdesk 单一登录](#configure-wdesk-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Wdesk 测试用户](#create-wdesk-test-user)** - 在 Wdesk 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Wdesk 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Wdesk”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤：

    ![Wdesk 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![Wdesk 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 配置 SSO 时，你会从 WDesk 门户获得这些值。

4. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Wdesk”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-wdesk-single-sign-on"></a>配置 Wdesk 单一登录

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Wdesk。

2. 在左下角，单击“管理员”，然后选择“管理员帐户”：
 
     ![配置单一登录](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. 在 Wdesk 管理员中，导航到“安全性”、“SAML”、“SAML 设置” > ：

    ![配置单一登录](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. 在“常规设置”下，选中“启用 SAML 单一登录”：

    ![配置单一登录](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. 在“服务提供程序详细信息”下，执行以下步骤：

    ![配置单一登录](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. 复制登录 URL 并将其粘贴到 Azure 门户的“登录 URL”文本框。
   
      b. 复制元数据 URL 并将其粘贴到 Azure 门户的“标识符”文本框。
       
      c. 复制使用者 URL 并将其粘贴到 Azure 门户的“回复 URL”文本框。
   
      d. 单击 Azure 门户的“保存”以保存更改。      

6. 单击“配置 IdP 设置”以打开“编辑 IdP 设置”对话框。 单击“选择文件”以查找从 Azure 门户保存的 Metadata.xml 文件，然后将其上传。
    
    ![配置单一登录](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. 单击“保存更改”。

    ![配置单一登录](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

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

在本部分中，通过授予 Britta Simon 访问 Wdesk 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Wdesk”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Wdesk”。

    ![“应用程序”列表中的“Wdesk”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-wdesk-test-user"></a>创建 Wdesk 测试用户

要使 Azure AD 用户能够登录到 Wdesk，必须将其预配到 Wdesk 中。 在“Wdesk”中，预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录到 Wdesk。

2. 导航到“管理员” > “管理员帐户”。

     ![配置单一登录](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. 单击“人员”下的“成员”。

4. 现在请单击“添加成员”以打开“添加成员”对话框。 
   
    ![创建 Azure AD 测试用户](./media/wdesk-tutorial/createuser1.png)  

5. 在“用户”文本框中输入用户的用户名（例如 brittasimon@contoso.com），然后单击“继续”按钮。

    ![创建 Azure AD 测试用户](./media/wdesk-tutorial/createuser3.png)

6.  输入详细信息，如下所示：
  
    ![创建 Azure AD 测试用户](./media/wdesk-tutorial/createuser4.png)
 
    a. 在“电子邮件”文本框中输入用户的电子邮件，例如 brittasimon@contoso.com。

    b. 在“名字”文本框中，输入用户的名字，如 Britta。

    c. 在“姓氏”文本框中，输入用户的名字，如 Simon。

7. 单击“保存成员”按钮。  

    ![创建 Azure AD 测试用户](./media/wdesk-tutorial/createuser5.png)

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Wdesk”磁贴时，应会自动登录到设置了 SSO 的 Wdesk。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


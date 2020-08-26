---
title: 教程：Azure Active Directory 与 Mitel Connect 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Mitel Connect 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/31/2020
ms.author: jeedes
ms.openlocfilehash: 63f1d1d40d8aff21641f3fa4ee10a289de40800d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552588"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect-or-cloudlink-platform"></a>教程：Azure Active Directory 与 Mitel MiCloud Connect 或 CloudLink Platform 的集成

在本教程中，你将了解如何使用 Mitel Connect 应用将 Azure Active Directory (Azure AD) 与 Mitel MiCloud Connect 或 CloudLink Platform 集成。 Azure Gallery 中提供了 Mitel Connect 应用。 将 Azure AD 与 MiCloud Connect 或 CloudLink Platform 集成，可提供以下优势：

* 你可以使用用户的企业凭据来控制用户对 Azure AD 中的 MiCloud Connect 应用和 CloudLink 应用的访问。
* 可让你帐户上的用户使用其 Azure AD 帐户自动登录到 MiCloud Connect 或 CloudLink（单一登录）。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

如果没有 Azure 订阅，请在开始将 Azure AD 与 Mitel MiCloud Connect 或 CloudLink Platform 集成之前，[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 MiCloud Connect 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* Mitel MiCloud Connect 帐户或 Mitel CloudLink 帐户，取决于你要配置的应用程序。

## <a name="scenario-description"></a>方案描述

在本教程中，我们将配置并测试 Azure AD 单一登录 (SSO)。

* Mitel Connect 支持 **SP** 发起的 SSO
* 配置 Mitel Connect 后，就可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-mitel-connect-from-the-gallery"></a>从库中添加 Mitel Connect

若要配置 Mitel Connect 与 Azure AD 的集成，需要使用 Azure 门户从库中将 Mitel Connect 添加到托管 SaaS 应用列表。

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择“Azure Active Directory”。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 依次选择“企业应用程序”、“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 选择“新建应用程序”。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索字段中键入“Mitel Connect”，在结果面板中选择“Mitel Connect”，然后选择“添加”  。

     ![结果列表中的“Mitel Connect”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，你将基于名为“Britta Simon”的测试用户为 MiCloud Connect 和 CloudLink Platform 配置并测试 Azure AD SSO。 若要运行单一登录，必须在 Azure AD 门户中的用户与 Mitel 平台上的相应用户之间建立链接关系。 有关为 MiCloud Connect 或 CloudLink Platform 配置和测试 Azure AD SSO 的信息，请参阅以下各节。
* 为 MiCloud Connect 配置和测试 Azure AD SSO
* 为 CloudLink Platform 配置和测试 Azure AD SSO

## <a name="configure-and-test-azure-ad-sso-with-micloud-connect"></a>为 MiCloud Connect 配置和测试 Azure AD SSO

若要为 MiCloud Connect 配置和测试 Azure AD 单一登录，请执行以下操作：

1. **[将 MiCloud Connect 配置为使用 Azure AD 进行单一登录](#configure-micloud-connect-for-sso-with-azure-ad)** - 让用户能够使用此功能并在应用程序端配置 SSO 设置。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
4. **[创建 Mitel MiCloud Connect 测试用户](#create-a-mitel-micloud-connect-test-user)** - 在 MiCloud Connect 帐户中创建 Britta Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

## <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>将 MiCloud Connect 配置为使用 Azure AD 进行单一登录

在本部分，我们将在 Azure 门户中为 MiCloud Connect 启用 Azure AD 单一登录，并将 MiCloud Connect 帐户配置为允许使用 Azure AD 进行单一登录。

若要为 MiCloud Connect 配置 Azure AD 单一登录，最简单的方法是并排打开 Azure 门户和 Mitel 帐户门户。 需要将一些信息从 Azure 门户复制到 Mitel 帐户门户，以及将一些信息从 Mitel 帐户门户复制到 Azure 门户。


1. 若要在 [Azure 门户](https://portal.azure.com/)中打开配置页，请执行以下操作：

    1. 在“Mitel Connect”应用程序集成页上，选择“单一登录” 。

       ![配置单一登录链接](common/select-sso.png)

    1. 在“选择单一登录方法”对话框中，选择“SAML” 。
    
       ![单一登录选择模式](common/select-saml-option.png)
    
       此时会显示基于 SAML 的登录页。

2. 若要在 Mitel 帐户门户中打开配置对话框，请执行以下操作：

    1. 在“电话系统”菜单中，选择“附加产品功能” 。

    1. 在“单一登录”右侧选择“激活”或“设置”  。
    
    此时会显示“Mitel Connect 单一登录设置”对话框。
    
3. 选中“启用单一登录”**** 复选框。
    
    ![显示选中了“启用单一登录”复选框的 Mitel Connect 单一登录设置页面的屏幕截图。](./media/mitel-connect-tutorial/mitel-connect-enable.png)

4. 在 Azure 门户的“基本 SAML 配置”部分，选择“编辑”图标 。
   
    ![图像](common/edit-urls.png)

    此时会显示“基本 SAML 配置”对话框。

5.  在 Mitel 帐户门户中复制“Mitel 标识符(实体 ID)”字段中的 URL，将其粘贴到 Azure 门户的“标识符(实体 ID)”字段中。********

6. 在 Mitel 帐户门户中复制“回复 URL (断言使用者服务 URL)”字段中的 URL，将其粘贴到 Azure 门户的“回复 URL (断言使用者服务 URL)”字段中。********

   ![图像](./media/mitel-connect-tutorial/mitel-azure-basic-configuration.png)

7. 在“登录 URL”文本框中，键入以下 URL 之一：****

    1. **https://portal.shoretelsky.com** - 将 Mitel 帐户门户用作默认的 Mitel 应用程序
    1. **https://teamwork.shoretel.com** - 将 Teamwork 用作默认的 Mitel 应用程序

    > [!NOTE]
    > 默认的 Mitel 应用程序是用户在访问面板中选择“Mitel Connect”磁贴时访问的应用程序。 也是从 Azure AD 进行测试设置时访问的应用程序。

8. 在 Azure 门户的“基本 SAML 配置”对话框中选择“保存” 。

9. 在 Azure 门户的“基于 SAML 的登录”页的“SAML 签名证书”部分，选择“证书(Base64)”旁边的“下载”，下载“签名证书”并将其保存到计算机    。

    ![图像](./media/mitel-connect-tutorial/azure-signing-certificate.png)

10. 在文本编辑器中打开签名证书文件，复制文件中的所有数据，然后将数据粘贴到 Mitel 帐户门户的“签名证书”字段中。**** 

      ![图像](./media/mitel-connect-tutorial/mitel-connect-signing-certificate.png)

11. 在 Azure 门户“基于 SAML 的登录”页的“设置 Mitel Connect”部分，执行以下操作： 

     1. 复制“登录 URL”字段中的 URL，将其粘贴到 Mitel 帐户门户的“登录 URL”字段中。********

     1. 复制“Azure AD 标识符”字段中的 URL，将其粘贴到 Mitel 帐户门户的“实体 ID”字段中。********
         
         ![图像](./media/mitel-connect-tutorial/mitel-azure-set-up-connect.png)

12. 在 Mitel 帐户门户的“Mitel Connect 单一登录设置”对话框中选择“保存” 。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

在本部分，我们将在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”对话框中，执行以下步骤：

    ![“用户”对话框](common/user-properties.png)

    1. 在“姓名”  字段中，键入输入“BrittaSimon”  。
  
    1. 在“用户名”字段中，键入 brittasimon@\<yourcompanydomain\>.\<extension\>。  例如，BrittaSimon@contoso.com。

    1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。********

    1. 选择“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 Mitel Connect 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在“应用程序”列表中，选择“Mitel Connect”。****

    ![“应用程序”列表中的“Mitel Connect”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。************

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”，然后选择屏幕底部的“选择”   。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后选择屏幕底部的“选择” 。

7. 在“添加分配”对话框中选择“分配”。

### <a name="create-a-mitel-micloud-connect-test-user"></a>创建 Mitel MiCloud Connect 测试用户

在本部分，我们将在 MiCloud Connect 帐户中创建名为 Britta Simon 的用户。 使用单一登录前，必须先创建并激活用户。

若要详细了解如何在 Mitel 帐户门户中添加用户，请参阅 Mitel 知识库中的 [Adding a User](https://oneview.mitel.com/s/article/Adding-a-User-092815)（添加用户）一文。

根据以下详细信息在 MiCloud Connect 帐户中创建用户：

* **名称：** Britta Simon
* **业务电子邮件地址：** `brittasimon@<yourcompanydomain>.<extension>`   
  （示例：[brittasimon@contoso.com](mailto:brittasimon@contoso.com)）
* **用户名：** `brittasimon@<yourcompanydomain>.<extension>`  
  （示例：[brittasimon@contoso.com](mailto:brittasimon@contoso.com)；用户的用户名通常就是用户的业务电子邮件地址）

> [!NOTE]
> 用户的 MiCloud Connect 用户名必须与用户在 Azure 中的电子邮件地址相同。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分，我们将使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择“Mitel Connect”磁贴时，系统会将你自动重定向，以便登录到 MiCloud Connect 应用程序。你已在“登录 URL”字段中将该应用程序配置为默认应用程序。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="configure-and-test-azure-ad-sso-with-cloudlink-platform"></a>为 CloudLink Platform 配置和测试 Azure AD SSO

本节介绍如何在 Azure 门户中为 CloudLink 平台启用 Azure AD SSO，以及如何配置 CloudLink 平台帐户，从而允许使用 Azure AD 进行单一登录。

若要为 CloudLink 平台配置 Azure AD 单一登录，建议并排打开 Azure 门户和 CloudLink 帐户门户，因为你需要在 Azure 门户和 CloudLink 帐户门户之间复制一些信息。

1. 若要在 [Azure 门户](https://portal.azure.com/)中打开配置页，请执行以下操作：

    1. 在“Mitel Connect”应用程序集成页上，选择“单一登录” 。

       ![配置单一登录链接](common/select-sso.png)

    1. 在“选择单一登录方法”对话框中，选择“SAML” 。

       ![单一登录选择模式](common/select-saml-option.png)
    
       此时会打开“基于 SAML 的登录”页面，其中显示“基本 SAML 配置”部分 。

       ![图像](./media/mitel-connect-tutorial/mitel-azure-saml-settings.png)

2. 若要访问 CloudLink 帐户门户中的“Azure AD 单一登录”配置面板，请执行以下操作：

    1. 转到要用来启用集成的客户帐户的“帐户信息”页面。

    1. 在“集成”部分中，选择“+ 新增” 。 弹出屏幕显示“集成”面板。

    1. 选择“第三方”选项卡。系统显示受支持的第三方应用程序的列表。 选择与“Azure AD 单一登录”关联的“添加”按钮，然后选择“完成”  。

       ![图像](./media/mitel-connect-tutorial/mitel-cloudlink-integrations.png)

       已为客户帐户启用“Azure AD 单一登录”，并将其添加到“帐户信息”页的“集成”部分  。   

   1. 选择“完成安装”。
    
      ![图像](./media/mitel-connect-tutorial/mitel-cloudlink-complete-setup.png)
      
      将打开“Azure AD 单一登录”配置面板。
      
       ![图像](./media/mitel-connect-tutorial/mitel-cloudlink-sso-setup.png)
       
       Mitel 建议不选中“可选 Mitel 凭据”部分的“启用 Mitel 凭据(可选)”复选框 。 仅当你希望用户在单一登录选项之外还能使用 Mitel 凭据登录 CloudLink 应用程序时，才选中此复选框。

3. 在 Azure 门户的“基于 SAML 的登录”页中，选择“基本 SAML 配置”部分中的“编辑”图标  。 此时将打开“基本 SAML 配置”面板。

    ![图像](./media/mitel-connect-tutorial/mitel-azure-saml-basic.png)
 
 4. 在 CloudLink 帐户门户中复制“Mitel 标识符(实体 ID)”字段中的 URL，将其粘贴到 Azure 门户的“标识符(实体 ID)”字段中 。

 5. 在 CloudLink 帐户门户中复制“回复 URL(断言使用者服务 URL)”字段中的 URL，将其粘贴到 Azure 门户的“回复 URL(断言使用者服务 URL)”字段中 。  
    
    ![图像](./media/mitel-connect-tutorial/mitel-cloudlink-saml-mapping.png) 

 6. 在“登录 URL”文本框中，键入 URL (`https://accounts.mitel.io`)，以将 CloudLink 帐户门户用作默认的 Mitel 应用程序。
     
     ![图像](./media/mitel-connect-tutorial/mitel-cloudlink-sign-on-url.png)
  
     > [!NOTE]
     > 默认的 Mitel 应用程序是用户在访问面板中选择“Mitel Connect”磁贴时打开的应用程序。 该应用程序也是用户从 Azure AD 配置测试设置时访问的应用程序。

7. 在“基本 SAML 配置”对话框中选择“保存” 。

8. 在 Azure 门户的“基于 SAML 的登录”页的“SAML 签名证书”部分，选择“证书(Base64)”旁边的“下载”，下载“签名证书”    。 将证书保存在计算机上。
  
    ![图像](./media/mitel-connect-tutorial/mitel-cloudlink-save-certificate.png)

9. 在文本编辑器中打开签名证书文件，复制文件中的所有数据，然后将数据粘贴到 CloudLink 帐户门户的“签名证书”字段中。  

    > [!NOTE]
    > 如果有多个证书，则建议将其逐个粘贴。 
       
    ![图像](./media/mitel-connect-tutorial/mitel-cloudlink-enter-certificate.png)

10. 在 Azure 门户的“基于 SAML 的登录”页的“设置 Mitel Connect”部分，执行以下操作： 

     1. 复制“登录 URL”字段中的 URL，将其粘贴到 CloudLink 帐户门户的“登录 URL”字段中 。

     1. 复制“Azure AD 标识符”字段中的 URL，将其粘贴到 CloudLink 帐户门户的“IDP 标识符(实体 ID)”字段中 。
     
        ![图像](./media/mitel-connect-tutorial/mitel-cloudlink-copy-settings.png)

11. 在 CloudLink 帐户门户的“Azure AD 单一登录”面板上选择“保存” 。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

在本部分，我们将在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”对话框中，执行以下步骤：

    ![“用户”对话框](common/user-properties.png)

    1. 在“姓名”**** 字段中，键入输入“BrittaSimon”****。
  
    1. 在“用户名”字段中，键入 brittasimon@\<yourcompanydomain\>.\<extension\>。  例如，BrittaSimon@contoso.com。

    1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。********

    1. 选择“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 Mitel Connect 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在“应用程序”列表中，选择“Mitel Connect”。****

    ![“应用程序”列表中的“Mitel Connect”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。************

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”，然后选择屏幕底部的“选择”   。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后选择屏幕底部的“选择” 。

7. 在“添加分配”对话框中选择“分配”。

### <a name="create-a-cloudlink-test-user"></a>创建 CloudLink 测试用户

本部分介绍如何在 CloudLink 平台上创建名为 Britta Simon 的测试用户。 必须先创建并激活用户，然后用户才能使用单一登录。

有关在 CloudLink 帐户门户中添加用户的详细信息，请参阅 [CloudLink 帐户文档](https://www.mitel.com/document-center/technology/cloudlink/all-releases/en/cloudlink-accounts-html)中的“管理用户”。

根据以下详细信息在 CloudLink 帐户门户中创建用户：

* 姓名：Britta Simon
* 名字：Britta
* 姓氏：Simon
* 电子邮件：BrittaSimon@contoso.com

> [!NOTE]
> 用户的 CloudLink 电子邮件地址必须与 Azure 门户中“用户主体名称”相同。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，你将使用访问面板测试 Azure AD SSO 配置。

在访问面板中选择“Mitel Connect”磁贴时，系统会将你自动重定向，以便登录到 CloudLink 应用程序。你已在“登录 URL”字段中将该应用程序配置为默认应用程序。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
---
title: 教程：Azure Active Directory 与 Mitel Connect 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Mitel Connect 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 30a4dcbd15d8a47d99b3a61879a7e61938d3b133
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160535"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>教程：Azure Active Directory 与 Mitel MiCloud Connect 的集成

本教程介绍如何将 Mitel MiCloud Connect 与 Azure Active Directory (Azure AD) 集成。 将 MiCloud Connect 与 Azure AD 集成具有以下优势：

* 可以在 Azure AD 中控制谁有权使用其企业凭据访问 MiCloud Connect 应用。
* 可以让你帐户上的用户使用其 Azure AD 帐户自动登录到 MiCloud Connect（单一登录）。


如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 MiCloud Connect 的集成，需要准备好以下各项：

* Azure AD 订阅

  如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* Mitel MiCloud Connect 帐户

## <a name="scenario-description"></a>方案描述

在本教程中，我们将配置并测试 Azure AD 单一登录 (SSO)。

* Mitel Connect 支持 **SP** 发起的 SSO

## <a name="adding-mitel-connect-from-the-gallery"></a>从库中添加 Mitel Connect

若要配置 Mitel Connect 与 Azure AD 的集成，需要使用 Azure 门户从库中将 Mitel Connect 添加到托管 SaaS 应用列表。

**若要从库中添加 Mitel Connect，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 单击“企业应用程序”，然后单击“所有应用程序”。  

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 单击“新建应用程序”。 

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索字段中键入“Mitel Connect”，在结果面板中单击“Mitel Connect”，然后单击“添加”。   

     ![结果列表中的“Mitel Connect”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，我们基于名为“Britta Simon”的测试用户为 MiCloud Connect 配置和测试 Azure AD 单一登录。  若要使单一登录有效，需要在 Azure AD 用户与 MiCloud Connect 相关用户之间建立关联。

若要为 MiCloud Connect 配置并测试 Azure AD 单一登录，需要完成以下步骤：

1. **[将 MiCloud Connect 配置为使用 Azure AD 进行单一登录](#configure-micloud-connect-for-sso-with-azure-ad)** - 让用户能够使用此功能并在应用程序端配置 SSO 设置。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
4. **[创建 Mitel MiCloud Connect 测试用户](#create-a-mitel-micloud-connect-test-user)** - 在 MiCloud Connect 帐户中创建 Britta Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>将 MiCloud Connect 配置为使用 Azure AD 进行单一登录

在本部分，我们将在 Azure 门户中为 MiCloud Connect 启用 Azure AD 单一登录，并将 MiCloud Connect 帐户配置为允许使用 Azure AD 进行单一登录。

若要为 MiCloud Connect 配置 Azure AD 单一登录，最简单的方法是并排打开 Azure 门户和 Mitel 帐户门户。 需要将一些信息从 Azure 门户复制到 Mitel 帐户门户，以及将一些信息从 Mitel 帐户门户复制到 Azure 门户。


1. 若要在 [Azure 门户](https://portal.azure.com/)中打开配置页，请执行以下操作：

    a. 在“Mitel Connect”应用程序集成页上，单击“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

    b. 在“选择单一登录方法”对话框中单击“SAML”。  

    ![单一登录选择模式](common/select-saml-option.png)
    
    此时会显示基于 SAML 的登录页。

2. 若要在 Mitel 帐户门户中打开配置对话框，请执行以下操作：

    a. 在“电话系统”菜单中单击“附加功能”。  

    b. 在“单一登录”右侧单击“激活”或“设置”。   
    
    此时会显示“Mitel Connect 单一登录设置”对话框。
    
3. 选中“启用单一登录”  复选框。
    ![图片](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. 在 Azure 门户的“基本 SAML 配置”部分，单击“编辑”图标。  
    ![图片](common/edit-urls.png)

    此时会显示“基本 SAML 配置”对话框。

5.  在 Mitel 帐户门户中复制“Mitel 标识符(实体 ID)”字段中的 URL，将其粘贴到 Azure 门户的“标识符(实体 ID)”字段中。  

6. 在 Mitel 帐户门户中复制“回复 URL (断言使用者服务 URL)”字段中的 URL，将其粘贴到 Azure 门户的“回复 URL (断言使用者服务 URL)”字段中。    
   ![图片](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. 在“登录 URL”文本框中，键入以下 URL 之一： 

    * **https://portal.shoretelsky.com** - 将 Mitel 帐户门户用作默认的 Mitel 应用程序
    * **https://teamwork.shoretel.com** - 将 Teamwork 用作默认的 Mitel 应用程序

    **注意**：默认 Mitel 应用程序是用户单击访问面板中的“Mitel Connect”磁贴时访问的应用程序， 也是从 Azure AD 进行测试设置时访问的应用程序。

8. 在 Azure 门户的“基本 SAML 配置”对话框中单击“保存”。  

9. 在 Azure 门户的“基于 SAML 的登录”页的“SAML 签名证书”部分，单击“证书(Base64)”旁边的“下载”，下载**签名证书**并将其保存到计算机     。
    ![图片](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. 在文本编辑器中打开签名证书文件，复制文件中的所有数据，然后将数据粘贴到 Mitel 帐户门户的“签名证书”字段中。  
    ![图片](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. 在 Azure 门户的“基于 SAML 的登录”页的“设置 Mitel Connect”部分，执行以下操作：  

    a. 复制“登录 URL”字段中的 URL，将其粘贴到 Mitel 帐户门户的“登录 URL”字段中。  

    b. 复制“Azure AD 标识符”字段中的 URL，将其粘贴到 Mitel 帐户门户的“实体 ID”字段中。  
    ![图片](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. 在 Mitel 帐户门户的“Mitel Connect 单一登录设置”对话框中单击“保存”。  

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

在本部分，我们将在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次单击“Azure Active Directory”、“用户”、“所有用户”。   

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 单击屏幕顶部的“新建用户”。 

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”对话框中，执行以下步骤：

    ![“用户”对话框](common/user-properties.png)

    a. 在“姓名”  字段中，键入输入“BrittaSimon”  。
  
    b. 在“用户名”字段中，键入 brittasimon@\<yourcompanydomain\>.\<extension\>。   
例如，BrittaSimon@contoso.com 。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 Mitel Connect 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中单击“企业应用程序”  ，然后单击“所有应用程序”  。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，单击“Mitel Connect”。 

    ![“应用程序”列表中的“Mitel Connect”链接](common/all-applications.png)

3. 在左侧菜单中，单击“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”，然后在“添加分配”对话框中单击“用户和组”    。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”，然后单击屏幕底部的“选择”。    

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”。  

7. 在“添加分配”对话框中，单击“分配”。  

### <a name="create-a-mitel-micloud-connect-test-user"></a>创建 Mitel MiCloud Connect 测试用户

在本部分，我们将在 MiCloud Connect 帐户中创建名为 Britta Simon 的用户。 使用单一登录前，必须先创建并激活用户。

若要详细了解如何在 Mitel 帐户门户中添加用户，请参阅 Mitel 知识库中的 [Adding a User](https://docs.shoretel.com/connectcloud/Account/Users/AddUser)（添加用户）一文。

根据以下详细信息在 MiCloud Connect 帐户中创建用户：

  * **名称：** Britta Simon

* **业务电子邮件地址：** `brittasimon@<yourcompanydomain>.<extension>`   
（示例：[brittasimon@contoso.com](mailto:brittasimon@contoso.com)）

* **用户名：** `brittasimon@<yourcompanydomain>.<extension>`  
（示例：[brittasimon@contoso.com](mailto:brittasimon@contoso.com)；用户的用户名通常就是用户的业务电子邮件地址）

**注意：** 用户的 MiCloud Connect 用户名必须与用户在 Azure 中的电子邮件地址相同。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分，我们将使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Mitel Connect”磁贴时，系统会将你自动重定向，以便登录到 MiCloud Connect 应用程序。你已在“登录 URL”字段中将该应用程序配置为默认应用程序。  有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 教程：Azure Active Directory 与 SAP Business Object Cloud 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SAP Business Object Cloud 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c64fb2dc4c494252f0078b8d23e05b15f07ebe00
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894557"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>教程：Azure Active Directory 与 SAP Business Object Cloud 的集成

本教程介绍了如何将 SAP Business Object Cloud 与 Azure Active Directory (Azure AD) 集成。
将 SAP Business Object Cloud 与 Azure AD 集成具有以下优势：

* 可以在 Azure AD 中控制谁有权访问 SAP Business Object Cloud。
* 可以让用户使用其 Azure AD 帐户自动登录到 SAP Business Object Cloud（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 SAP Business Object Cloud 的集成，需备齐以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了单一登录的 SAP Business Object Cloud 订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* SAP Business Object Cloud 支持 **SP** 发起的 SSO

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>从库中添加 SAP Business Object Cloud

若要配置 SAP Business Object Cloud 与 Azure AD 的集成，需要从库中将 SAP Business Object Cloud 添加到托管 SaaS 应用列表。

若要从库中添加 SAP Business Object Cloud，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“SAP Business Object Cloud”，在结果面板中选择“SAP Business Object Cloud”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 SAP Business Object Cloud](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为 **Britta Simon** 的测试用户配置和测试 SAP Business Object Cloud 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 SAP Business Object Cloud 中相关用户之间建立链接关系。

若要配置和测试 SAP Business Object Cloud 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 SAP Business Object Cloud 单一登录](#configure-sap-business-object-cloud-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 SAP Business Object Cloud 测试用户](#create-sap-business-object-cloud-test-user)** - 在 SAP Business Object Cloud 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 SAP Business Object Cloud 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **SAP Business Object Cloud** 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![SAP Business Object Cloud 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：

    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：
    
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > 这些 URL 中的值仅用于演示。 请使用实际登录 URL 和标识符 URL 来更新这些值。 若要获取登录 URL，请联系 [SAP Business Object Cloud 客户端支持团队](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/)。 可以通过从管理员控制台下载 SAP Business Object Cloud 元数据来获取标识符 URL。 本教程后面将对有所解释。

4. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

### <a name="configure-sap-business-object-cloud-single-sign-on"></a>配置 SAP Business Object Cloud 单一登录

1. 在其他 Web 浏览器窗口中，以管理员身份登录到 SAP Business Object Cloud 公司站点。

2. 选择“菜单” > “系统” > “管理”。
    
    ![依次选择“菜单”、“系统”和“管理”](./media/sapboc-tutorial/config1.png)

3. 在“安全性”选项卡上，选择“编辑”（笔形）图标。
    
    ![在“安全性”选项卡上，选择“编辑”图标](./media/sapboc-tutorial/config2.png)  

4. 选择“SAML 单一登录 (SSO)”作为“身份验证方法”。

    ![选择“SAML 单一登录 (SSO)”作为身份验证方法](./media/sapboc-tutorial/config3.png)  

5. 若要下载服务提供程序元数据（步骤 1），请选择“下载”。 在元数据文件中，查找并复制“entityID”值。 在 Azure 门户中，在“基本 SAML 配置”对话框中，将该值粘贴到“标识符”框中。

    ![复制并粘贴 entityID 值](./media/sapboc-tutorial/config4.png)  

6. 若要上传从 Azure 门户中下载的文件中的服务提供者元数据（步骤 2），请在“上传标识提供者元数据”下，选择“上传”。  

    ![在“上传标识提供者元数据”下，选择“上传”](./media/sapboc-tutorial/config5.png)

7. 在“用户属性”列表中，选择要用于实现的用户属性（步骤 3）。 此用户属性将映射到标识提供程者。 若要在用户页上输入自定义属性，请使用“自定义 SAML 映射”选项。 或者，可以选择“电子邮件”或“用户 ID”作为用户属性。 在示例中，我们选择的是“电子邮件”，因为我们映射的用户标识符声明包含 Azure 门户中“用户属性和声明”部分的“userprincipalname”属性。 这提供了一个唯一的用户电子邮件，它将被发送到 SAP Business Object Cloud 应用程序作为每个成功的 SAML 响应。

    ![选择用户属性](./media/sapboc-tutorial/config6.png)

8. 若要验证标识提供者的帐户（步骤 4），请在“登录凭据（电子邮件）”框中，输入用户的电子邮件地址。 然后，选择“验证帐户”。 系统会将登录凭据添加到用户帐户。

    ![输入电子邮件，并选择验证帐户](./media/sapboc-tutorial/config7.png)

9. 选择“保存”图标。

    ![“保存”图标](./media/sapboc-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 SAP Business Object Cloud 的权限，允许使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“SAP Business Object Cloud”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“SAP Business Object Cloud”。

    ![应用程序列表中的 SAP Business Object Cloud 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-sap-business-object-cloud-test-user"></a>创建 SAP Business Object Cloud 测试用户

为了使 Azure AD 用户能够登录到 SAP Business Object Cloud，必须将其预配到 SAP Business Object Cloud 中。 在 SAP Business Object Cloud 中，预配属手动任务。

若要预配用户帐户：

1. 以管理员身份登录到 SAP Business Object Cloud 公司站点。

2. 选择“菜单” > “安全” > “用户”。

    ![添加员工](./media/sapboc-tutorial/user1.png)

3. 在“用户”页上，若要添加新用户详细信息，请选择 +。 

    ![添加用户页](./media/sapboc-tutorial/user4.png)

    然后完成以下步骤：

    a. 在“用户 ID”框中，输入用户的用户 ID，如 Britta。

    b. 在“名字”框中，输入用户的名字，如 Britta。

    c. 在“姓氏”框中，输入用户的姓氏，如 Simon。

    d. 在“显示名称”框中，输入用户的全名，如 Britta Simon。

    e. 在“电子邮件”框中，输入用户的电子邮件地址，例如 **brittasimon\@contoso.com**。

    f. 在“选择角色”页上，选择适当的用户角色，然后选择“确定”。

      ![选择角色](./media/sapboc-tutorial/user3.png)

    g. 选择“保存”图标。    

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 SAP Business Object Cloud 磁贴时，应当会自动登录到你为其设置了 SSO 的 SAP Business Object Cloud。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 教程：Azure Active Directory 与 SAP Analytics Cloud 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 SAP Analytics Cloud 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd01c86086c7491d22f655fcba5da237286412f
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347796"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>教程：将 SAP Analytics Cloud 与 Azure Active Directory 集成

本教程介绍如何将 SAP Analytics Cloud 与 Azure Active Directory (Azure AD) 集成。 将 SAP Analytics Cloud 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 SAP Analytics Cloud。
* 让用户使用其 Azure AD 帐户自动登录到 SAP Analytics Cloud。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 SAP Analytics Cloud 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* SAP Analytics Cloud 支持 **SP** 发起的 SSO

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>从库中添加 SAP Analytics Cloud

若要配置 SAP Analytics Cloud 与 Azure AD 的集成，需要从库中将 SAP Analytics Cloud 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **SAP Analytics Cloud**。 
1. 从结果面板中选择“SAP Analytics Cloud”，然后添加该应用。  在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 SAP Analytics Cloud 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 SAP Analytics Cloud 中的相关用户之间建立链接关系。

若要配置并测试 SAP Analytics Cloud 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. **[配置 SAP Analytics Cloud SSO](#configure-sap-analytics-cloud-sso)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
5. **[创建 SAP Analytics Cloud 测试用户](#create-sap-analytics-cloud-test-user)** - 在 SAP Analytics Cloud 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“SAP Analytics Cloud”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL  ：

    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL  ：
    
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > 这些 URL 中的值仅用于演示。 请使用实际登录 URL 和标识符 URL 来更新这些值。 若要获取登录 URL，请联系 [SAP Analytics Cloud 客户端支持团队](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/)。 可以通过从管理员控制台下载 SAP Analytics Cloud 元数据来获取标识符 URL。 本教程后面将对有所解释。

4. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 SAP Analytics Cloud”部分中，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>配置 SAP Analytics Cloud SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 SAP Analytics Cloud 公司站点。

2. 选择“菜单”   > “系统”   > “管理”  。
    
    ![依次选择“菜单”、“系统”和“管理”](./media/sapboc-tutorial/config1.png)

3. 在“安全性”  选项卡上，选择“编辑”  （笔形）图标。
    
    ![在“安全性”选项卡上，选择“编辑”图标](./media/sapboc-tutorial/config2.png)  

4. 选择“SAML 单一登录 (SSO)”  作为“身份验证方法”  。

    ![选择“SAML 单一登录 (SSO)”作为身份验证方法](./media/sapboc-tutorial/config3.png)  

5. 若要下载服务提供程序元数据（步骤 1），请选择“下载”  。 在元数据文件中，查找并复制“entityID”  值。 在 Azure 门户中，在“基本 SAML 配置”  对话框中，将该值粘贴到“标识符”  框中。

    ![复制并粘贴 entityID 值](./media/sapboc-tutorial/config4.png)  

6. 若要上传从 Azure 门户中下载的文件中的服务提供者元数据（步骤 2），请在“上传标识提供者元数据”  下，选择“上传”  。  

    ![在“上传标识提供者元数据”下，选择“上传”](./media/sapboc-tutorial/config5.png)

7. 在“用户属性”  列表中，选择要用于实现的用户属性（步骤 3）。 此用户属性将映射到标识提供程者。 若要在用户页上输入自定义属性，请使用“自定义 SAML 映射”  选项。 或者，可以选择“电子邮件”  或“用户 ID”  作为用户属性。 在示例中，我们选择的是“电子邮件”  ，因为我们映射的用户标识符声明包含 Azure 门户中“用户属性和声明”  部分的“userprincipalname”  属性。 这提供了一个唯一的用户电子邮件，它将在每个成功的 SAML 响应中发送到 SAP Analytics Cloud 应用程序。

    ![选择用户属性](./media/sapboc-tutorial/config6.png)

8. 若要验证标识提供者的帐户（步骤 4），请在“登录凭据（电子邮件）”  框中，输入用户的电子邮件地址。 然后，选择“验证帐户”  。 系统会将登录凭据添加到用户帐户。

    ![输入电子邮件，并选择验证帐户](./media/sapboc-tutorial/config7.png)

9. 选择“保存”  图标。

    ![“保存”图标](./media/sapboc-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 SAP Analytics Cloud 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“SAP Analytics Cloud”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-sap-analytics-cloud-test-user"></a>创建 SAP Analytics Cloud 测试用户

必须先在 SAP Analytics Cloud 中预配 Azure AD 用户，然后他们才能登录到 SAP Analytics Cloud。 在 SAP Analytics Cloud 中，预配是一项手动任务。

若要预配用户帐户：

1. 以管理员身份登录到 SAP Analytics Cloud 公司站点。

2. 选择“菜单”   > “安全”   > “用户”  。

    ![添加员工](./media/sapboc-tutorial/user1.png)

3. 在“用户”  页上，若要添加新用户详细信息，请选择 +  。 

    ![添加用户页](./media/sapboc-tutorial/user4.png)

    然后完成以下步骤：

    a. 在“用户 ID”框中，输入用户的用户 ID，例如 **B**。 

    b. 在“名字”框中，输入用户的名字，例如 **B**。 

    c. 在“姓氏”  框中，输入用户的姓氏，如 Simon  。

    d. 在“显示名称”框中，输入用户的全名，例如 **B.Simon**。 

    e. 在“电子邮件”  框中，输入用户的电子邮件地址，例如 `b.simon@contoso.com`。

    f. 在“选择角色”  页上，选择适当的用户角色，然后选择“确定”  。

      ![选择角色](./media/sapboc-tutorial/user3.png)

    g. 选择“保存”  图标。

### <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“SAP Analytics Cloud”磁贴时，应会自动登录到设置了 SSO 的 SAP Analytics Cloud。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


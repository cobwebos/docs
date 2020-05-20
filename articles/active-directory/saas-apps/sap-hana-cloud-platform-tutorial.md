---
title: 教程：将 Azure Active Directory 与 SAP Cloud Platform 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SAP Cloud Platform 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89ea2c45e16dfeb63801f70fa4480c0d865a890f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160087"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>教程：将 Azure Active Directory 与 SAP Cloud Platform 集成

在本教程中，了解如何将 SAP Cloud Platform 与 Azure Active Directory (Azure AD) 集成。
将 SAP Cloud Platform 与 Azure AD 集成具有以下优势：

* 可以在 Azure AD 中控制谁有权访问 SAP Cloud Platform
* 可以让用户使用其 Azure AD 帐户自动登录到 SAP Cloud Platform（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 SAP Cloud Platform 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 SAP Cloud Platform 单一登录的订阅

完成本教程后，已向 SAP Cloud Platform 分配的 Azure AD 用户能够使用[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)单一登录到该应用程序。

>[!IMPORTANT]
>用户需要部署自己的应用程序或订阅 SAP Cloud Platform 帐户上的应用程序来测试单一登录。 在本教程中，会在帐户中部署应用程序。
> 

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* SAP Cloud Platform 支持 SP 发起的 SSO 

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>从库中添加 SAP Cloud Platform

若要配置 SAP Cloud Platform 与 Azure AD 的集成，需要从库中将 SAP Cloud Platform 添加到托管 SaaS 应用列表。

**若要从库中添加 SAP Cloud Platform，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“SAP Cloud Platform”，在结果面板中选择“SAP Cloud Platform”，然后单击“添加”按钮添加该应用程序    。

     ![结果列表中的 SAP Cloud Platform](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于名为 Britta Simon 的测试用户配置并测试 SAP Cloud Platform 的 Azure AD 单一登录  。
若要运行单一登录，需要在 Azure AD 用户与 SAP Cloud Platform 相关用户之间建立链接关系。

若要配置和测试 SAP Cloud Platform 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 SAP Cloud Platform 单一登录](#configure-sap-cloud-platform-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 SAP Cloud Platform 测试用户](#create-sap-cloud-platform-test-user)** - 在 SAP Cloud Platform 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 SAP Cloud Platform 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)的 SAP Cloud Platform 应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![SAP Cloud Platform 域和 URL 单一登录信息](common/sp-identifier-reply.png)

    a. 在“登录 URL”  文本框中，键入用户用于登录“SAP Cloud Platform”应用程序的 URL  。 这是“SAP Cloud Platform”应用程序中受保护资源的特定于帐户的 URL。 URL 基于以下模式：`https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >这是“SAP Cloud Platform”应用程序中要求用户进行身份验证的 URL。
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. 在“标识符”文本框，使用以下模式之一键入 SAP Cloud Platform 的 URL：  

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. 在“回复 URL”  文本框中，使用以下模式键入 URL：

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [SAP Cloud Platform 客户端支持团队](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html)获取登录 URL 和标识符。 可以从“信任管理”部分获取回复 URL，本教程稍后将会介绍。
    > 
4. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>配置 SAP Cloud Platform 单一登录

1. 在另一个 Web 浏览器窗口中，登录到 SAP Cloud Platform 考核中心，网址为 `https://account.<landscape host>.ondemand.com/cockpit`（例如： https://account.hanatrial.ondemand.com/cockpit) ）。

2. 单击“信任”  选项卡。
   
    ![信任](./media/sap-hana-cloud-platform-tutorial/ic790800.png "信任")

3. 在“信任管理”部分中的“本地服务提供程序”下，执行以下步骤： 

    ![信任管理](./media/sap-hana-cloud-platform-tutorial/ic793931.png "信任管理")
   
    a. 单击 **“编辑”** 。

    b. 对于“配置类型”  ，选择“自定义”  。

    c. 对于“本地提供程序名称”  ，保留默认值。 复制此值并将其粘贴到 SAP Cloud Platform Azure AD 配置的“标识符”字段中。 

    d. 若要生成**签名密钥**和**签名证书**密钥对，请单击“生成密钥对”  。

    e. 对于“主体传播”  ，选择“禁用”  。

    f. 对于“强制身份验证”  ，选择“禁用”  。

    g. 单击“保存”  。

4. 保存“本地服务提供程序”设置后，执行以下步骤获取回复 URL： 
   
    ![获取元数据](./media/sap-hana-cloud-platform-tutorial/ic793930.png "获取元数据")

    a. 单击“获取元数据”下载 SAP Cloud Platform 元数据文件  。

    b. 打开下载的 SAP Cloud Platform 元数据 XML 文件，并找到 **ns3:AssertionConsumerService** 标记。
 
    c. 复制 **Location** 属性的值，并将其粘贴到 SAP Cloud Platform Azure AD 配置中的“回复 URL”  字段。

5. 单击“受信任的标识提供者”  选项卡，并单击“添加受信任的标识提供者”  。
   
    ![信任管理](./media/sap-hana-cloud-platform-tutorial/ic790802.png "信任管理")
   
    >[!NOTE]
    >若要管理受信任的标识提供者的列表，需要已在本地服务提供程序部分中选择自定义配置类型。 如果选择“默认”配置类型，则对 SAP ID 服务有不可编辑的隐式信任。 如果选择“无”，则不具有任何信任设置。
    > 
    > 

6. 单击“常规”  选项卡，并单击“浏览”  以上载已下载的元数据文件。
    
    ![信任管理](./media/sap-hana-cloud-platform-tutorial/ic793932.png "信任管理")
    
    >[!NOTE]
    >上传元数据文件后，会自动填充“单一登录 URL”、“单一注销 URL”和“签名证书”的值。   
    > 
     
7. 单击“属性”选项卡  。

8. 在“属性”  选项卡中，执行以下步骤：
    
    ![属性](./media/sap-hana-cloud-platform-tutorial/ic790804.png "属性") 

    a. 单击“添加基于断言的属性”  ，并添加以下基于断言的属性：
       
    | 断言属性 | 主体属性 |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |名 |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |姓 |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |电子邮件 |
   
     >[!NOTE]
     >属性的配置取决于如何开发 SCP 上的应用程序，即它们应在 SAML 响应中预期哪些属性，以及它们在哪个名称（主体属性）下通过代码访问此属性。
     > 
    
    b. 屏幕截图中的**默认属性**仅用于说明目的。 它并不是使方案正常工作所必需的。  
 
    c. 屏幕截图中所示的**主体属性**的名称和值取决于如何开发应用程序。 很可能应用程序需要不同的映射。

### <a name="assertion-based-groups"></a>基于断言的组

可选步骤是，可以为 Azure Active Directory 标识提供者配置基于断言的组。

使用 SAP Cloud Platform 上的组可将一个或多个用户动态分配到 SAP Cloud Platform 应用程序中的一个或多个角色，具体由 SAML 2.0 断言中的属性值确定。 

例如，如果断言包含属性“contract=temporary”，你可能希望将所有受影响的用户添加到组“TEMPORARY”   。 组“TEMPORARY”可能包含 SAP Cloud Platform 帐户中部署的一个或多个应用程序中的一个或多个角色  。
 
若想要同时将多个用户分配到 SAP Cloud Platform 帐户中的应用程序的一个或多个角色，请使用基于断言的组。 如果只想将一个或少量用户分配到特定角色，建议直接在 SAP Cloud Platform 考核中心的“授权”  选项卡上分配它们。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 SAP Cloud Platform 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“SAP Cloud Platform”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“SAP Cloud Platform”  。

    ![应用程序列表中的“SAP Cloud Platform”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-sap-cloud-platform-test-user"></a>创建 SAP Cloud Platform 测试用户

要使 Azure AD 用户能够登录 SAP Cloud Platform，必须为他们分配 SAP Cloud Platform 中的角色。

**若要向用户分配角色，请执行以下步骤：**

1. 登录到 **SAP Cloud Platform** 考核中心。

2. 执行以下操作：
   
    ![授权](./media/sap-hana-cloud-platform-tutorial/ic790805.png "授权")
   
    a. 单击“授权”  。

    b. 单击“用户”  选项卡。

    c. 在“用户”  文本框中，键入用户的电子邮件地址。

    d. 单击“分配”  ，将用户分配到角色。

    e. 单击“保存”  。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 SAP Cloud Platform 磁贴时，应会自动登录到为其设置了 SSO 的 SAP Cloud Platform。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


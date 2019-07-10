---
title: 教程：Azure Active Directory 与 Rackspace SSO 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Rackspace SSO 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 36b398be-2f7e-4ce8-9031-53587299bc4a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 31826f5d4d88c977f859a009bface2fddf3a1c88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093183"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>教程：Azure Active Directory 与 Rackspace SSO 集成

在本教程中，你将了解如何将 Rackspace SSO 与 Azure Active Directory (Azure AD) 进行集成。
将 Rackspace SSO 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Rackspace SSO。
* 可让用户使用其 Azure AD 帐户自动登录到 Rackspace SSO（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Rackspace SSO 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 Rackspace SSO 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Rackspace SSO 支持 **SP** 发起的 SSO

## <a name="adding-rackspace-sso-from-the-gallery"></a>从库中添加 Rackspace SSO

要配置 Rackspace SSO 与 Azure AD 的集成，需要从库中将 Rackspace SSO 添加到托管 SaaS 应用列表。

**若要从库中添加 Rackspace SSO，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“Rackspace SSO”  ，在结果面板中选择“Rackspace SSO”  ，单击“添加”  按钮添加该应用程序。

     ![结果列表中的 Rackspace SSO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Rackspace SSO 的 Azure AD 单一登录。
将单一登录用于 Rackspace 时，首次登录到 Rackspace 门户时，将自动创建 Rackspace 用户。 

若要配置和测试 Rackspace SSO 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Rackspace SSO 单一登录](#configure-rackspace-sso-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[在 Rackspace 控制面板中设置属性映射](#set-up-attribute-mapping-in-the-rackspace-control-panel)** - 为 Azure AD 用户分配 Rackspace 角色。
1. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Rackspace SSO 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Rackspace SSO”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  中，上传你可以从 [URL](https://login.rackspace.com/federate/sp.xml) 下载的**服务提供程序元数据文件**，然后执行以下步骤：

    a. 单击“上传元数据文件”  。

    ![image](common/upload-metadata.png)

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    ![image](common/browse-upload-metadata.png)

    c. 成功上传元数据文件后，将自动填充所需的 URL。

    d. 在“登录 URL”文本框中，键入 URL：  `https://login.rackspace.com/federate/`

    ![Rackspace SSO 域和 URL 单一登录信息](common/sp-signonurl.png)   

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

此文件将上传到 Rackspace 来填充所需的联合身份验证配置设置。

### <a name="configure-rackspace-sso-single-sign-on"></a>配置 Rackspace SSO 单一登录

若要在 **Rackspace SSO** 端配置单一登录，请执行以下操作：

1. 请参阅文档[向控制面板中添加标识提供者](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. 它将引导你完成以下步骤：
    1. 创建新的标识提供者
    1. 指定在登录时用户将用来标识你的公司的电子邮件域。
    1. 上传之前从 Azure 控制面板下载的**联合元数据 XML**。

这将正确配置 Azure 和 Rackspace 进行连接所需的基本 SSO 设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中键入 `brittasimon@yourcompanydomain.extension`。  例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予对 Rackspace SSO 的访问权限使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Rackspace SSO”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Rackspace SSO”  。

    ![应用程序列表中的 Rackspace SSO 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>在 Rackspace 控制面板中设置属性映射

Rackspace 使用**属性映射策略**为你的单一登录用户分配 Rackspace 角色和组。 **属性映射策略**将 Azure AD SAML 声明转换为 Rackspace 所需的用户配置字段。 可以在 Rackspace [属性映射基础知识文档](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/)中找到更多文档。 一些注意事项：

* 如果希望使用 Azure AD 组分配可变级别的 Rackspace 访问权限，则需要在 Azure **Rackspace SSO** 单一登录设置中启用组声明。 然后，可以使用**属性映射策略**将那些组匹配到所需的 Rackspace 角色和组：

    ![组声明设置](common/sso-groups-claim.png)

* 默认情况下，Azure AD 会在 SAML 声明中发送 Azure AD 组的 UID 而非组名称。 但是，如果要将本地 Active Directory 同步到 Azure AD，则可以选择发送组的实际名称：

    ![组声明名称设置](common/sso-groups-claims-names.png)

下面的示例**属性映射策略**演示了以下内容：
1. 将 Rackspace 用户的名称设置为 `user.name` SAML 声明。 可以使用任何声明，但最常见的做法是将其设置为包含用户电子邮件地址的字段。
1. 通过按组名称或组 UID 匹配 Azure AD 组来为用户设置 Rackspace 角色 `admin` 和 `billing:admin`。 `roles` 字段中使用了替代项 `"{0}"`，它将替换为 `remote` 规则表达式的结果。 
1. 使用 `"{D}"` 默认替代项  让 Rackspace 通过在 SAML 交换中查找标准且已知的 SAML 声明来检索额外的 SAML 字段。

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> 在编辑策略文件时，请确保使用可以验证 YAML 语法的文本编辑器。

有关更多示例，请参阅 Rackspace [属性映射基础知识文档](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/)。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Rackspace SSO”磁贴时，应会自动登录到设置了 SSO 的 Rackspace SSO。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

还可以使用 **Rackspace SSO** 单一登录设置中的“验证”  按钮：

   ![SSO“验证”按钮](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


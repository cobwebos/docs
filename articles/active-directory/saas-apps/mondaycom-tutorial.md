---
title: 教程：Azure Active Directory 与 monday.com 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 monday.com 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc142bf02a44ea85861f4cc648fd7ee8602c7520
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65896879"
---
# <a name="tutorial-azure-active-directory-integration-with-mondaycom"></a>教程：Azure Active Directory 与 monday.com 集成

本教程介绍如何将 monday.com 与 Azure Active Directory (Azure AD) 集成。

将 monday.com 与 Azure AD 集成提供以下优势：

* 可以使用 Azure AD 控制有权限访问 monday.com 的人员。
* 用户可使用其 Azure AD 帐户（单一登录）自动登录到 monday.com。
* 可在一个中心位置（即 Azure 门户）管理帐户。

有关服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅[单一登录到 Azure Active Directory 的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 monday.com 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果还没有 Azure AD 订阅，可在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录的 monday.com 订阅。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中配置和测试 Azure AD 单一登录，并将 monday.com 与 Azure AD 集成。

monday.com 支持以下功能：

* **SP 发起的单一登录**
* **IDP 发起的单一登录**
* **实时用户预配**

## <a name="add-mondaycom-in-the-azure-portal"></a>在 Azure 门户中添加 monday.com

若要将 monday.com 与 Azure AD 集成，必须将 monday.com 添加到托管 SaaS 应用列表中。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左侧菜单中，选择“Azure Active Directory”  。

    ![“Azure Active Directory”选项](common/select-azuread.png)

1. 选择“企业应用程序” > “所有应用程序”   。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 若要添加应用程序，请选择“新建应用程序”  。

    ![“新建应用程序”选项](common/add-new-app.png)

1. 在搜索框中输入“monday.com”  。 在搜索结果中选择“monday.com”，并选择“添加”   。

    ![结果列表中的 monday.com](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 monday.com 的 Azure AD 单一登录  。 若要正常使用单一登录，需要在 Azure AD 用户与 monday.com 相关用户之间建立链接关系。

若要配置和测试 monday.com 的 Azure AD 单一登录，必须完成以下构建基块：

| 任务 | 说明 |
| --- | --- |
| **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** | 使用户能够使用此功能。 |
| **[配置 monday.com 单一登录](#configure-mondaycom-single-sign-on)** | 在应用程序中配置单一登录设置。 |
| **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** | 测试名为 Britta Simon 的用户的 Azure AD 单一登录。 |
| **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** | 使 Britta Simon 能够使用 Azure AD 单一登录。 |
| **[创建 monday.com 测试用户](#create-a-mondaycom-test-user)** | 在 monday.com 中创建一个与 Azure AD 中的 Britta Simon 相对应的关联用户。 |
| **[测试单一登录](#test-single-sign-on)** | 验证配置是否正常工作。 |

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中使用 monday.com 配置 Azure AD 单一登录。

1. 在 [Azure 门户](https://portal.azure.com/)中，在 monday.com 应用程序集成窗格上，选择“单一登录”   。

    ![配置单一登录选项](common/select-sso.png)

1. 在“选择单一登录方法”窗格中，选择“SAML”或“SAML/WS-Fed”模式以启用单一登录    。

    ![单一登录选择模式](common/select-saml-option.png)

1. 在“设置 SAML 单一登录”窗格中，选择“编辑”（铅笔图标）以打开“基本 SAML 配置”窗格    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”窗格中，如果服务提供商元数据文件，并且要配置 IDP 发起模式，请完成以下步骤   ：

    1. 选择“上传元数据文件”  。

       ![“上传元数据文件”选项](common/upload-metadata.png)

    1. 要选择元数据文件，请选择文件夹图标，然后选择“上传”  。

       ![选择元数据文件，然后选择“上传”按钮](common/browse-upload-metadata.png)

    1. 成功上传元数据文件后，“标识符”和“回复 URL”值会自动填充在“基本 SAML 配置”窗格    ：

       ![“基本 SAML 配置”窗格中的 IDP 值](common/idp-intiated.png)

       > [!Note]
       > 如果未自动填充“标识符”和“回复 URL”，请手动输入值   。

1. 在 SP 发起模式中配置应用程序  ：

    1. 选择“设置其他 URL”  。
    
    1. 在“登录 URL”框中，输入具有以下模式的 URL：https:\//\<your-domain>.monday.com  。 联系 [ 客户端支持团队](mailto:support@monday.com)以获取登录 URL。

        ![“设置其他 URL”选项](common/metadata-upload-additional-signon.png)

1. monday.com 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 要管理这些属性值，请在“使用 SAML 设置单一登录”窗格中，选择“编辑”以打开“用户属性”窗格    。

    ![“用户属性”窗格](common/edit-attribute.png)

1. 在“用户声明”下，选择“编辑”以编辑声明   。 若要添加声明，请选择“添加新声明”，然后配置 SAML 令牌属性，如上图所示  。 然后完成以下步骤： 

    1. 选择“添加新声明”  。

        ![“用户声明”窗格中的“添加新声明”选项](common/new-save-attribute.png)

    1. 在“管理用户声明”窗格中，设置以下值  ：
        
       1. 在“名称”框中，键入为用户声明行显示的属性名称  。

       1. 将“命名空间”留空。 

       1. 对于“源”，请选择“属性”。  

       1. 在“源属性”列表中，选择为用户声明行显示的属性值  。

       1. 选择“确定”，然后选择“保存”   。

       ![管理用户声明](common/new-attribute-details.png)

1. 在“使用 SAML 设置单一登录”窗格的“SAML 签名证书”下，选择“证书 (Base64)”旁边的“下载”     。 根据需要选择下载选项。 将证书保存在计算机上。

    ![证书 (Base64) 下载选项](common/certificatebase64.png)

1. 在“设置 monday.com”部分，根据要求复制以下 URL  ：

    * 登录 URL
    * Azure AD 标识符
    * 注销 URL

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-mondaycom-single-sign-on"></a>配置 monday.com 单一登录

若要在 monday.com 端配置单一登录，请将下载的“证书(Base64)”文件以及从 Azure 门户复制的相关 URL 发送到 [monday.com 支持团队](mailto:support@monday.com)。 monday.com 支持团队使用你发送的信息来确保两端都正确设置 SAML 单一登录连接。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，会在 Azure 门户中创建名为“Britta Simon”的测试用户。

1. 在 Azure 门户中，选择“Azure Active Directory” > “用户” > “所有用户”。   

    ![“用户”和“所有用户”选项](common/users.png)

1. 选择“新建用户”。 

    ![“新建用户”选项](common/new-user.png)

1. 在“用户”窗格中完成以下步骤： 

    1. 在“姓名”  框中，输入 **BrittaSimon**。
  
    1. 在“用户名”框中，输入“brittasimon\@\<your-company-domain>.\<extension>”   。 例如，brittasimon\@contoso.com  。

    1. 选中“显示密码”复选框  。 记下“密码”框中显示的值  。

    1. 选择“创建”  。

    ![“用户”窗格](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 monday.com 的权限，以便她可使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序” > “monday.com”    。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 在应用程序列表中，选择“monday.com”  。

    ![应用程序列表中的 monday.com](common/all-applications.png)

1. 在菜单中选择“用户和组”  。

    ![“用户和组”选项](common/users-groups-blade.png)

1. 选择“添加用户”。  然后，在“添加分配”窗格中选择“用户和组”。  

    ![“添加分配”窗格](common/add-assign-user.png)

1. 在“用户和组”窗格中，在用户列表中选择“Britta Simon”   。 选择“选择”。 

1. 如果希望在 SAML 断言中使用角色值，请在“选择角色”窗格中，从列表中为用户选择相关的角色  。 选择“选择”。 

1. 在“添加分配”窗格中选择“分配”   。

### <a name="create-a-mondaycom-test-user"></a>创建 monday.com 测试用户

在本部分中，将在 monday.com 应用程序中创建名为 Britta Simon 的用户。 monday.com 支持默认启用的恰时用户预配。 此部分不存在任何操作项。 如果 monday.com 中不存在用户，则会在身份验证后创建一个新用户。

### <a name="test-single-sign-on"></a>测试单一登录

本部分将使用“我的应用”门户测试 Azure AD 单一登录配置。

设置单一登录后，在“我的应用”门户中选择“monday.com”后，将自动登录到 monday.com  。 有关“我的应用”门户的详细信息，请参阅[访问和使用“我的应用”门户上的应用](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

若要了解更多信息，请查看下列文章：

- [用于将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

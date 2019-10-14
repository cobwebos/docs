---
title: 教程：Azure Active Directory 与 Costpoint 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Costpoint 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01168540e27605db0d240c0774159a710b5d5254
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840091"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>教程：将 Costpoint 与 Azure Active Directory 集成

本教程介绍如何将 Costpoint 与 Azure Active Directory (Azure AD) 集成。 将 Costpoint 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Costpoint。
* 让用户使用其 Azure AD 帐户自动登录到 Costpoint。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Costpoint 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程将在测试环境中配置并测试 Azure AD SSO。 Costpoint 支持 **SP 和 IDP** 发起的 SSO。

## <a name="generate-costpoint-metadata"></a>生成 Costpoint 元数据

**DeltekCostpoint711Security** 指南中介绍了 Costpoint SAML SSO 配置。 请从 Deltek Costpoint 支持站点下载该指南，并参阅“SAML 单一登录设置” > “在 Costpoint 与 Microsoft Azure 之间配置 SAML 单一登录”部分。   遵照说明生成“Costpoint SP 联合元数据 XML”文件。  

![Costpoint 配置实用工具](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>从库中添加 Costpoint

若要将 Costpoint 与 Azure AD 集成，请先从 Azure 门户的库中将 Costpoint 添加到托管 SaaS 应用的列表：

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。

1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。

   ![“Azure Active Directory”按钮](common/select-azuread.png)

1. 选择“企业应用程序” > “所有应用程序”   。

   ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

1. 若要添加新的应用程序，请选择“新建应用程序”  。

   ![“新增应用程序”按钮](common/add-new-app.png)

1. 在“从库中添加”部分的搜索框中，输入“Costpoint”   。

   ![结果列表中的 Costpoint](common/search-new-app.png)

1. 在结果列表中选择“Costpoint”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>配置和测试 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Costpoint 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Costpoint 相关用户之间建立链接关系。

若要配置和测试 Costpoint 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
1. **[配置 Costpoint](#configure-costpoint)** ，以在应用程序端配置 SAML SSO 设置。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以使用 B.Simon 测试 Azure AD 单一登录。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 B.Simon 能够使用 Azure AD 单一登录。
1. **[创建 Costpoint 测试用户](#create-a-costpoint-test-user)** - 在 Costpoint 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO：

1. 在“Costpoint”应用程序集成页上，选择“单一登录”。  

   ![配置单一登录链接](common/select-sso.png)

1. 在“基本 SAML 配置”  部分，如果有服务提供程序元数据文件  ，请完成以下步骤：

   > [!NOTE]
   > 可以在[生成 Costpoint 元数据](#generate-costpoint-metadata)中获取服务提供程序元数据文件。 本教程后面会介绍如何使用该文件。
 
   1. 依次选择“上传元数据文件”按钮、以前通过 Costpoint 生成的“Costpoint SP 联合元数据 XML”文件、用于上传文件的“添加”按钮。   

      ![上传元数据文件](./media/costpoint-tutorial/upload-metadata.png)
    
   1. 成功上传元数据文件后，“标识符”和“回复 URL”值会自动填充到“Costpoint”部分中。  

      > [!NOTE]
      > 如果“标识符”  和“回复 URL”  值未自动填充，则请按要求手动输入这些值。 验证是否已正确设置“标识符(实体 ID)”和“回复 URL(断言使用者服务 URL)”，以及“ACS URL”是否是以 **/LoginServlet.cps** 结尾的有效 Costpoint URL。   

   1. 选择“设置其他 URL”  。 对于“中继状态”  ，请按以下模式输入一个值：`system=[your system]`（例如 **system=DELTEKCP**）。

1. 在“使用 SAML 设置单一登录”页上的“SAML 签名证书”部分，选择“复制”图标，以复制“应用联合元数据 URL”，并将其保存到记事本     。

   ![SAML 签名证书](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>配置 Costpoint

1. 返回到 Costpoint 配置实用程序。 在“IdP 联合元数据 XML”文本框中，粘贴“应用联合元数据 URL”文件的内容。   

   ![Costpoint 配置实用工具](./media/costpoint-tutorial/config-utility-idp.png)

1. 继续按 **DeltekCostpoint711Security.pdf** 指南中的说明操作，完成 Costpoint SAML 设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为“B.Simon”的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。   

   ![“用户和组”以及“所有用户”链接](common/users.png)

1. 选择“新建用户”。 

   ![“新建用户”按钮](common/new-user.png)

1. 在“用户”属性中，完成以下步骤： 

   ![“用户”对话框](common/user-properties.png)

   1. 在“姓名”字段中输入 **B.Simon**。 
   
   1. 在“用户名”字段中输入 `b.simon\@yourcompanydomain.extension`  （例如 B.Simon@contoso.com）。
   
   1. 选中“显示密码”复选框，然后记下“密码”字段中显示的值。  
   
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 B.Simon 访问 Costpoint 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。  

1. 在应用程序列表中，选择“Costpoint”。 

1. 在应用概述页的“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”。  在“添加分配”对话框中选择“用户和组”。  

   ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中的“用户”列表内，选择“B.Simon”。    然后选择“选择”。 

1. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”对话框的列表中为用户选择相应的角色，然后选择“选择”。  

1. 在“添加分配”对话框中选择“分配”。  

### <a name="create-a-costpoint-test-user"></a>创建 Costpoint 测试用户

在本部分，我们在 Costpoint 中创建一个用户。 假设用户 ID 为“B.SIMON”，用户的姓名为“B.Simon”。   在 [Costpoint 支持团队](https://www.deltek.com/about/contact-us)的配合下，将用户添加到 Costpoint 平台。 必须先创建并激活用户，然后用户才能使用单一登录。

创建用户后，必须选择“Active Directory”作为用户的“身份验证方法”，并且必须选中“SAML 单一登录”复选框，同时 Azure Active Directory 中的用户名必须是“Active Directory 或证书 ID”（如以下屏幕截图所示）。    

![Costpoint 用户](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>测试 SSO

选择访问面板中的“Costpoint”磁贴时，应当会自动登录到 Costpoint 应用程序，因为已设置 SSO。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

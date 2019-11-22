---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 PureCloud by Genesys 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 PureCloud by Genesys 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 779328f4c21afb4392663e6f8840749ea505c529
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242436"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>教程：Azure Active Directory 单一登录 (SSO) 与 PureCloud by Genesys 集成

本教程介绍如何将 PureCloud by Genesys 与 Azure Active Directory (Azure AD) 集成。 完成此操作后，你可以：

* 使用 Azure AD 控制哪些用户可以通过 Genesys 访问 PureCloud。
* 让用户使用其 Azure AD 帐户自动登录到 PureCloud by Genesys。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有帐户，可以获取 [免费帐户](https://azure.microsoft.com/free/)。
* 已启用 PureCloud by Genesys 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* PureCloud by Genesys 支持 SP 和 IDP 发起的 SSO  。

> [!NOTE]
> 由于此应用程序的 ID 是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>从库中添加 PureCloud by Genesys

若要配置 PureCloud by Genesys 与 Azure AD 的集成，必须从库中将 PureCloud by Genesys 添加到托管 SaaS 应用程序列表。 为此，请执行以下步骤：

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 转到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“PureCloud by Genesys”   。
1. 从结果面板中选择“PureCloud by Genesys”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>配置和测试 PureCloud by Genesys 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 PureCloud by Genesys 的 Azure AD SSO  。 若要运行 SSO，必须在 Azure AD 用户与 PureCloud by Genesys 相关用户之间建立链接关系。

若要配置和测试 PureCloud by Genesys 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以使用 B.Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 B.Simon 能够使用 Azure AD 单一登录。
1. **[配置 PureCloud by Genesys SSO](#configure-purecloud-by-genesys-sso)** 以在应用程序端配置单一登录设置。
    1. **[创建 PureCloud by Genesys 测试用户](#create-purecloud-by-genesys-test-user)** 以在 PureCloud by Genesys 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

若要在 Azure 门户中启用 Azure AD SSO，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)的“PureCloud by Genesys”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 若要在 IDP 发起模式下配置应用程序，请在“基本 SAML 配置”部分输入以下字段的值：  

    a. 在“标识符”框中，输入与你所在区域对应的 URL  ：

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. 在“回复 URL”框中，输入与你所在区域对应的 URL  ：

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. 若要将应用程序配置为 SP 发起的模式，请选择“设置其他 URL”并执行以下步骤：  

    在“登录 URL”框中，输入与你所在区域对应的 URL  ：
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. PureCloud by Genesys 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表：

    ![image](common/default-attributes.png)

1. 此外，PureCloud by Genesys 应用程序还要求在 SAML 响应中传递一些属性，如下表所示。 这些属性也是预先填充的，但可以根据需要查看它们。

    | Name | 源属性|
    | ---------------| --------------- |
    | 电子邮件 | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 PureCloud by Genesys”部分中，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，你将在 Azure 门户中创建名为 B. Simon 的测试用户：

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”、“所有用户”。   
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名字段”中，按以下格式输入用户名：username@companydomain.extension  。 例如：`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值   。
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 B.Simon 访问 PureCloud by Genesys 的权限，设置其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“PureCloud by Genesys”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，选择“用户”列表中的“B.Simon”，然后选择屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后选择屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，选择“分配”按钮。  

## <a name="configure-purecloud-by-genesys-sso"></a>配置 PureCloud by Genesys SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 PureCloud by Genesys。

1. 选择顶部的“管理员”，然后转到“集成”下的“单一登录”    。

    ![配置单一登录](./media/purecloud-by-genesys-tutorial/configure01.png)

1. 切换到“ADFS/Azure AD(Premium)”选项卡，然后执行以下这些步骤  ：

    ![配置单一登录](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. 选择“浏览”，将从 Azure 门户下载的 base-64 编码证书上传到“ADFS 证书”   。

    b. 在“ADFS 颁发者 URI”框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    c. 在“目标 URI”框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    d. 对于“信赖方标识符”值，转到 Azure 门户，然后在 PureCloud by Genesys 应用程序集成页中，选择“属性”选项卡并复制“应用程序 ID”值     。 然后将其粘贴到“信赖方标识符”框中  。

    ![配置单一登录](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. 选择“保存”。 

### <a name="create-purecloud-by-genesys-test-user"></a>创建 PureCloud by Genesys 测试用户

若要使 Azure AD 用户能够登录 PureCloud by Genesys，必须将其预配到 PureCloud by Genesys 中。 在 PureCloud by Genesys 中，需手动完成预配任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 PureCloud by Genesys。

1. 选择顶部的“管理员”并转到“人员和权限”下的“人员”    。

    ![配置单一登录](./media/purecloud-by-genesys-tutorial/configure03.png)

1. 在“人员”页上，选择“添加人员”   。

    ![配置单一登录](./media/purecloud-by-genesys-tutorial/configure04.png)

1. 在“将人员添加到组织”对话框中，执行以下这些步骤  ：

    ![配置单一登录](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. 在“全名”  框中，输入用户的姓名。 例如：B.simon  .

    b. 在“电子邮件”框中，输入用户的电子邮件  。 例如：b.simon\@contoso.com  。

    c. 选择“创建”  。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

选择“访问面板”中的“PureCloud by Genesys”磁贴时，应当会自动登录到为其设置了 SSO 的 PureCloud by Genesys 帐户  。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用程序与 Azure AD 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure AD 中的应用程序访问和单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure AD 中的条件访问是什么？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 PureCloud by Genesys](https://aad.portal.azure.com/)
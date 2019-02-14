---
title: 教程：Azure Active Directory 与 Infor CloudSuite 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Infor CloudSuite 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a2f4f843-00d2-4522-a29d-6496cc5a781a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b61aece1b569e0e1022b9d239f2fe55a75c766d7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56161747"
---
# <a name="tutorial-azure-active-directory-integration-with-infor-cloudsuite"></a>教程：Azure Active Directory 与 Infor CloudSuite 集成

在本教程中，了解如何将 Infor CloudSuite 与 Azure Active Directory (Azure AD) 集成。

将 Infor CloudSuite 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权限访问 Infor CloudSuite.
- 可让用户使用其 Azure AD 帐户自动登录到 Infor CloudSuite（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Infor CloudSuite 的集成，需要以下项：

- Azure AD 订阅
- 启用了 Infor CloudSuite 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Infor CloudSuite
2. 配置和测试 Azure AD 单一登录

## <a name="adding-infor-cloudsuite-from-the-gallery"></a>从库添加 Infor CloudSuite

若要配置 Infor CloudSuite 与 Azure AD 的集成，需要从库中将 Infor CloudSuite 添加到托管 SaaS 应用列表。

若要从库中添加 Infor CloudSuite，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“Infor CloudSuite”，在结果面板中选择“Infor CloudSuite”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Infor CloudSuite](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 Infor CloudSuite 的 Azure AD 单一登录。

若要使用单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Infor CloudSuite 用户。 换句话说，需要在 Azure AD 用户与 Infor CloudSuite 中的相关用户之间建立链接关系。

若要配置和测试 Infor CloudSuite 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Infor CloudSuite 测试用户](#creating-infor-cloudsuite-test-user) - 在 Infor CloudSuite 中创建 Britta Simon 的对应用户，将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将介绍如何在 Azure 门户中启用 Azure AD 单一登录并在 Infor CloudSuite 应用程序中配置单一登录。

若要配置 Infor CloudSuite 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的“Infor CloudSuite”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![配置单一登录](common/tutorial-general-301.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![配置单一登录](common/editconfigure.png)

4. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    ![Infor CloudSuite 域和 URL 单一登录信息](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-url1.png)

    a. 在“标识符”文本框中，键入一个 URL：
    
    | | |
    |-|-|
    | ` http://mingle-sso.inforcloudsuite.com`|
    | `http://mingle-sso.se1.inforcloudsuite.com`|
    | `http://mingle-sso.eu1.inforcloudsuite.com`|
    | `http://mingle-sso.se2.inforcloudsuite.com`|
    | |

    b. 在“回复 URL”文本框中键入 URL：

    | | |
    |-|-|
    | ` https://mingle-sso.inforcloudsuite.com:443/sp/ACS.saml2 `|
    | `https://mingle-sso.se1.inforcloudsuite.com:443/sp/ACS.saml2 `|
    | `https://mingle-sso.se2.inforcloudsuite.com:443/sp/ACS.saml2 `|
    | `https://mingle-sso.eu1.inforcloudsuite.com:443/sp/ACS.saml2`|
    | |

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![Infor CloudSuite 域和 URL 单一登录信息](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-url2.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：
    
    | | |
    |-|-|
    | `https://mingle-portal.inforcloudsuite.com/Tenant-Name/`|
    | `https://mingle-portal.eu1.inforcloudsuite.com/Tenant-Name/`|
    | `https://mingle-portal.se1.inforcloudsuite.com/Tenant-Name/ `|
    | `https://mingle-portal.se2.inforcloudsuite.com/Tenant-Name/`| 

    > [!NOTE]
    > 登录 URL 值不是真实值。 使用实际登录 URL 更新此值。 若要获取此值，请与 [Infor CloudSuite 客户端支持团队](mailto:support@infor.com)联系。

6. 在“SAML 签名证书”页的“SAML 签名证书”部分，单击“下载”以下载“联合元数据 XML”并将元数据文件保存在计算机上。

    ![证书下载链接](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-certificate.png)

7. 若要在 Infor CloudSuite 端配置单一登录，需将下载的“联合元数据 XML”发送给 [Infor CloudSuite 支持团队](mailto:support@infor.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](common/create-aaduser-01.png) 

3. 在“用户属性”中，按照以下步骤操作。

    ![创建 Azure AD 测试用户](common/create-aaduser-02.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="creating-infor-cloudsuite-test-user"></a>创建 Infor CloudSuite 测试用户

本部分的目的是在 Infor CloudSuite 中创建名为“Britta Simon”的用户。 Infor CloudSuite 支持可由租户管理员启用的实时预配。此部分不存在任何操作项。 尝试访问 Infor CloudSuite 期间，如果尚不存在用户，则会创建一个新用户。

> [!Note]
> 如果需要手动创建用户，请联系  [Infor CloudSuite 支持团队](mailto:support@infor.com)。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Infor CloudSuite 的权限，以支持其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“Infor CloudSuite”。

    ![配置单一登录](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Infor CloudSuite”磁贴，即可自动登录到 Infor CloudSuite 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png

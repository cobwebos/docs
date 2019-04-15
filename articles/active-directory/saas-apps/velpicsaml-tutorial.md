---
title: 教程：Azure Active Directory 与 Velpic SAML 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Velpic SAML 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed1c201831b1edf4f5c267d2093ada4ba91d7df
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263309"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>教程：Azure Active Directory 与 Velpic SAML 的集成

本教程介绍如何将 Velpic SAML 与 Azure Active Directory (Azure AD) 集成。
将 Velpic SAML 与 Azure AD 集成具有以下优势：

* 可以在 Azure AD 中控制谁有权访问 Velpic SAML。
* 可让用户使用其 Azure AD 帐户自动登录到 Velpic SAML（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Velpic SAML 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 Velpic SAML 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Velpic SAML 支持 **SP** 发起的 SSO

## <a name="adding-velpic-saml-from-the-gallery"></a>从库添加 Velpic SAML

要配置 Velpic SAML 与 Azure AD 的集成，需要从库中将 Velpic SAML 添加到托管 SaaS 应用列表。

**若要从库中添加 Velpic SAML，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **Velpic SAML**，在结果面板中选择“Velpic SAML”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的“Velpic SAML”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Velpic SAML 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Velpic SAML 相关用户之间建立链接关系。

若要配置并测试 Velpic SAML 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Velpic SAML 单一登录](#configure-velpic-saml-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Velpic SAML 测试用户](#create-velpic-saml-test-user)** - 在 Velpic SAML 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Velpic SAML 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Velpic SAML”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![Velpic SAML 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL： `https://<sub-domain>.velpicsaml.net`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL： `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > 请注意，“登录 URL”由 Velpic SAML 团队提供，而“标识符”值会在完成 Velpic SAML 侧的 SSO 插件配置后可供使用。 需要从 Velpic SAML 应用程序页复制该值，将其粘贴到此处。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Velpic SAML”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-velpic-saml-single-sign-on"></a>配置 Velpic SAML 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Velpic SAML 公司站点。

2. 单击“管理”选项卡，转到“集成”部分，在这里需单击“插件”按钮，创建用于登录的新插件。

    ![插件](./media/velpicsaml-tutorial/velpic_1.png)

3. 单击“添加插件”按钮。
    
    ![插件](./media/velpicsaml-tutorial/velpic_2.png)

4. 单击“添加插件”页中的“SAML”磁贴。
    
    ![插件](./media/velpicsaml-tutorial/velpic_3.png)

5. 输入新 SAML 插件的名称，并单击“添加”按钮。

    ![插件](./media/velpicsaml-tutorial/velpic_4.png)

6. 输入详细信息，如下所示：

    ![插件](./media/velpicsaml-tutorial/velpic_5.png)

    a. 在“名称”文本框中，键入 SAML 插件的名称。

    b. 在“颁发者 URL”文本框中，粘贴从 Azure 门户的“配置登录”窗口复制的“Azure AD 标识符”。

    c. 在“提供程序元数据配置”中，上传从 Azure 门户下载的元数据 XML 文件。

    d. 也可通过启用“自动创建新用户”复选框，来选择启用 SAML 实时预配。 如果用户不存在于 Velpic 中且该标记未启用，则从 Azure 登录会失败。 如果该标记已启用，则用户会在登录时自动预配到 Velpic 中。 

    e. 从文本框复制“单一登录 URL”，然后将其粘贴到 Azure 门户中。
    
    f. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 `brittasimon@yourcompanydomain.extension`。 例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 Velpic SAML 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Velpic SAML”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Velpic SAML”。

    ![“应用程序”列表中的“Velpic SAML”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-velpic-saml-test-user"></a>创建 Velpic SAML 测试用户

此步骤通常不是必需的，因为应用程序支持实时用户预配。 如果未启用自动用户预配，则可手动创建用户，如下所述。

以管理员身份登录到 Velpic SAML 公司站点，并执行以下步骤：
    
1. 单击“管理”选项卡，转到“用户”部分，并单击“新建”按钮添加用户。

    ![添加用户](./media/velpicsaml-tutorial/velpic_7.png)

2. 在“新建用户”对话框页中，执行以下步骤。

    ![user](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. 在“名字”文本框中键入名字 Britta。

    b. 在“姓氏”文本框键入姓氏 Simon。

    c. 在“用户名”文本框中，键入 Britta Simon 的用户名。

    d. 在“电子邮件”文本框中，键入 Brittasimon@contoso.com 帐户的电子邮件地址。

    e. 其余信息为可选，可根据需要填写。
    
    f. 单击“保存”。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

1. 单击访问面板中的 Velpic SAML 磁贴时，应显示 Velpic SAML 应用程序的登录页。 应该在登录页上显示“使用 Azure AD 登录”按钮。

    ![插件](./media/velpicsaml-tutorial/velpic_6.png)

1. 单击“使用 Azure AD 登录”按钮，使用 Azure AD 帐户登录到 Velpic。

## <a name="additional-resources"></a>其他资源

- [ 有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表 ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？ ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory 中的条件访问是什么？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


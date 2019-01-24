---
title: 教程：Azure Active Directory 与 iProva 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 iProva 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: ba3b0e06630665082b62e070dac64e8bc572f6dc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811700"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>教程：Azure Active Directory 与 iProva 集成

本教程介绍如何将 iProva 与 Azure Active Directory (Azure AD) 集成。
将 iProva 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 iProva。
* 可以让用户使用其 Azure AD 帐户自动登录到 iProva（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 iProva 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 iProva 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* iProva 支持 SP 发起的 SSO

## <a name="adding-iprova-from-the-gallery"></a>从库中添加 iProva

若要配置 iProva 与 Azure AD 的集成，需要从库中将 iProva 添加到托管 SaaS 应用列表。

**若要从库中添加 iProva，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“iProva”，在结果面板中选择“iProva”，然后单击“添加”按钮添加应用程序。

     ![结果列表中的 iProva](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为 Britta Simon 的测试用户使用 iProva 配置和测试 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 iProva 相关用户之间建立链接关系。

若要配置和测试 iProva 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[从 iProva 中检索配置信息](#retrieve-configuration-information-from-iprova)** - 为后续步骤做准备。
2. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
3. **[配置 iProva 单一登录](#configure-iprova-single-sign-on)** - 在应用程序端配置单一登录。
4. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
5. [创建 iProva 测试用户](#create-iprova-test-user) - 在 iProva 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
7. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="retrieve-configuration-information-from-iprova"></a>从 iProva 检索配置信息

在本部分中，将从 iProva 检索部分基本信息。
需要此信息才能配置 Azure AD 单一登录

1. 打开 Web 浏览器并使用以下 URL 模式导航到 iProva 中的 SAML2 信息页面：

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

     ![查看 iProva SAML2 信息页](media/iprova-tutorial/iprova-saml2-info.png)

2. 在另一个浏览器选项卡中继续执行后续步骤时，保持浏览器选项卡处于打开状态。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 iProva 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，在 iProva 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“标识符”字段中填写 iProva SAML2 信息页上标签“EntityID”后面显示的值（仍在其他浏览器标签页中打开）。

    b. 在“Reply-URL”字段中填写 iProva SAML2 信息页上标签“Reply-URL”后面显示的值（仍在其他浏览器标签页中打开）。

    c. 在“登录 URL”字段中填写 iProva SAML2 信息页上标签“登录 URL”后面显示的值（仍在其他浏览器标签页中打开）。

    ![iProva 域和 URL 单一登录信息](common/sp-identifier-reply.png)

5. iProva 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性”对话框。

    ![图像](common/edit-attribute.png)

6. 在“用户属性”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    | 名称 | 源属性| 命名空间 |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

    a. 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 在“命名空间”列表中，键入为该行显示的命名空间值。

    d. 选择“源”作为“属性”。

    e. 在“源属性”列表中，键入为该行显示的属性值。

    f. 单击“确定”

    g. 单击“ **保存**”。

7. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，单击“复制”按钮，以复制“应用联合元数据 URL”，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>配置 iProva 单一登录

1. 使用管理员帐户登录 iProva。

2. 打开“转到”菜单。

3. 单击“应用程序管理”。

4. 单击“系统设置”面板中的“常规”。

5. 单击“编辑”。

6. 向下滚动到“访问控制”。

    ![iProva 访问控制设置](media/iprova-tutorial/iprova-accesscontrol.png)

7. 查找设置“用户使用其网络帐户自动登录”，并将其更改为“是，通过 SAML 进行身份验证”。 现在将出现其他选项。

8. 单击“设置”按钮。

9. 单击“下一步”按钮。

10. iProva 现在会询问是否要从 URL 下载联合数据，或者从文件上传数据。 选择“从 URL”选项。

    ![下载 Azure AD 元数据](media/iprova-tutorial/iprova-download-metadata.png)

11. 现在，粘贴在“配置 Azure AD 单一登录”章节的最后一步中保存的元数据 URL。

12. 单击箭头状按钮，从 Azure AD 下载元数据。

13. 下载完成后，将显示确认消息“已下载有效的联合数据文件”。

14. 单击“下一步”按钮。

15. 暂时跳过“测试登录”选项，然后单击“下一步”按钮。

16. 在名为“声明使用”的下拉列表中，选择 windowsaccountname 。

17. 单击“完成”按钮。

18. 现在返回“编辑常规设置”屏幕。 向下滚动到页面底部，单击“确定”按钮保存配置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中输入名称，如 BrittaSimon。
  
    b. 在“用户名”字段中键入 yourname@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 iProva 的访问权限支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“iProva”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“iProva”。

    ![应用程序列表中的 iProva 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-iprova-test-user"></a>创建 iProva 测试用户

1. 使用管理员帐户登录 iProva。

2. 打开“转到”菜单。

3. 单击“应用程序管理”。

4. 单击“用户和用户组”面板中的“用户”。

5. 单击“添加”按钮。

6. 在“用户名”字段中，键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

7. 在“全名”字段中输入完整名称，如 BrittaSimon。

8. 选择“无密码(使用单一登录)”选项。

9. 在“电子邮件地址”字段中输入 yourname@yourcompanydomain.extension，例如，BrittaSimon@contoso.com

10. 向下滚动到页面底部，然后单击“完成”按钮。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 iProva 磁贴时，应会自动登录到为其设置了 SSO 的 iProva。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [iProva - 如何配置 SAML2 单一登录](https://webshare.iprova.nl/0wqwm45yn09f5poh/Document.aspx)
---
title: 教程：Azure Active Directory 与 FreshDesk 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 FreshDesk 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e75a45bc4ccd2614520fda39e09996c2286bdbd0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227619"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>教程：Azure Active Directory 与 FreshDesk 集成

在本教程中，了解如何将 FreshDesk 与 Azure Active Directory (Azure AD) 集成。
将 FreshDesk 与 Azure AD 集成具有以下优势：

* 可以在 Azure AD 中控制谁有权访问 FreshDesk。
* 可以让用户使用其 Azure AD 帐户自动登录到 FreshDesk（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 FreshDesk 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 FreshDesk 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* FreshDesk 支持 **SP** 发起的 SSO

## <a name="adding-freshdesk-from-the-gallery"></a>从库中添加 FreshDesk

要配置 FreshDesk 与 Azure AD 的集成，需要从库中将 FreshDesk 添加到托管 SaaS 应用列表。

**若要从库中添加 FreshDesk，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“FreshDesk”，在结果面板中选择“FreshDesk”，然后单击“添加”按钮添加该应用程序。   

     ![结果列表中的 FreshDesk](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”  的测试用户配置和测试 FreshDesk 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 FreshDesk 相关用户之间建立链接关系。

若要配置和测试 FreshDesk 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 FreshDesk 单一登录](#configure-freshdesk-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 FreshDesk 测试用户](#create-freshdesk-test-user)** - 在 FreshDesk 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 FreshDesk 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **FreshDesk** 应用程序集成页上，选择“单一登录”  。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![FreshDesk 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<tenant-name>.freshdesk.com`，或者键入 Freshdesk 建议的其他值。

    b. 在“标识符(实体 ID)”  文本框中，使用以下模式键入 URL：`https://<tenant-name>.freshdesk.com`，或者键入 Freshdesk 建议的其他值。

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 若要获取这些值，请与 [FreshDesk 客户端支持团队](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg)联系。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. FreshDesk 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示一个示例。 “唯一用户标识符”的默认值是“user.userprincipalname”，但 FreshDesk 要求通过用户的电子邮件地址映射此项   。 为此，可以使用列表中的 **user.mail** 属性，或使用基于组织配置的相应属性值。 

    ![image](common/edit-attribute.png)

6. 在“用户属性”对话框的“用户声明”部分中，通过使用“编辑图标”编辑声明或使用“添加新声明”添加声明，按上图所示配置 SAML 令牌属性，并执行以下步骤     ：
    
    | 名称 | 源属性 |
    | ---------------| --------------- |
    | 唯一用户标识符 | user.mail |

    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“确定” 

    g. 单击“保存”  。

7. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

8. 打开**命令提示符**并运行以下命令：

    a. 在命令提示符中输入 `certutil.exe -dump FreshDesk.cer` 值。

    > [!NOTE]
    > 此处，**FreshDesk.cer** 是从 Azure 门户下载的证书。

    b. 复制**证书哈希(sha256)** 值并将其粘贴到记事本中。 

9. 在“设置 FreshDesk”部分中，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-freshdesk-single-sign-on"></a>配置 FreshDesk 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Freshdesk 公司站点。

2. 选择**设置图标**，在“安全性”  部分中，执行以下步骤：

    ![单一登录](./media/freshdesk-tutorial/IC776770.png "单一登录")
  
    a. 对于“单一登录(SSO)”，请选择“启用”。  

    b. 选择“SAML SSO”。 

    c. 在“SAML 登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    d. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    e. 在“安全证书指纹”  文本框中，粘贴你之前获得的**证书哈希(sha256)** 值。
  
    f. 单击“保存”  。

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

在本部分中，通过向 Britta Simon 授予对 FreshDesk 的访问权限使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”  、“所有应用程序”  和“FreshDesk”  。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“FreshDesk”。 

    ![应用程序列表中的 FreshDesk 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-freshdesk-test-user"></a>创建 FreshDesk 测试用户

为使 Azure AD 用户能够登录到 FreshDesk，必须将其预配到 FreshDesk。  
就 FreshDesk 而言，预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到“Freshdesk”  租户。

2. 在顶部菜单中，单击“管理员”。 

    ![管理员](./media/freshdesk-tutorial/IC776772.png "管理员")

3. 在“常规设置”选项卡上，单击“代理”。  
  
    ![代理](./media/freshdesk-tutorial/IC776773.png "代理")

4. 单击“新建代理”。 

    ![新建代理](./media/freshdesk-tutorial/IC776774.png "新建代理")

5. 在“代理信息”对话框中，执行以下步骤：

    ![代理信息](./media/freshdesk-tutorial/IC776775.png "代理信息")

    a. 在“电子邮件”  文本框中，键入要预配的 Azure AD 帐户的 Azure AD 电子邮件地址。

    b. 在“完整名称”文本框中，键入要预配的 Azure AD 帐户的名称  。

    c. 在“名称”  文本框中，键入要预配的 Azure AD 帐户的名称。

    d. 单击“保存”  。

    >[!NOTE]
    >Azure AD 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
    >
    >[!NOTE]
    >可以使用任何其他 Freshdesk 用户帐户创建工具或 Freshdesk 提供的 API 来将 Azure AD 用户帐户预配到 FreshDesk。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 FreshDesk 磁贴时，应当会自动登录到已为其设置了 SSO 的 FreshDesk。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


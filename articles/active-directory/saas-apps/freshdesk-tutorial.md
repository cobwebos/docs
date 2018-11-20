---
title: 教程：Azure Active Directory 与 FreshDesk 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 FreshDesk 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2018
ms.author: jeedes
ms.openlocfilehash: b5968b83fc9beb481e2ad2c0cd44d2c284747fa1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2018
ms.locfileid: "51010807"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>教程：Azure Active Directory 与 FreshDesk 集成

在本教程中，了解如何将 FreshDesk 与 Azure Active Directory (Azure AD) 集成。

将 FreshDesk 与 Azure AD 集成具有以下优势：

- 可以在 Azure AD 中控制谁有权访问 FreshDesk。
- 可以让用户使用其 Azure AD 帐户自动登录到 FreshDesk（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 FreshDesk 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了单一登录的 FreshDesk 订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 FreshDesk
2. 配置和测试 Azure AD 单一登录

## <a name="adding-freshdesk-from-the-gallery"></a>从库中添加 FreshDesk

要配置 FreshDesk 与 Azure AD 的集成，需要从库中将 FreshDesk 添加到托管 SaaS 应用列表。

**若要从库中添加 FreshDesk，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“FreshDesk”，在结果面板中选择“FreshDesk”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 FreshDesk](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 FreshDesk 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 FreshDesk 用户。 换句话说，需要建立 Azure AD 用户与 FreshDesk 中相关用户之间的关联关系。

若要配置和测试 FreshDesk 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 FreshDesk 测试用户](#creating-a-freshdesk-test-user)** - 在 FreshDesk 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 FreshDesk 应用程序中配置单一登录。

**若要配置 FreshDesk 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **FreshDesk** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![配置单一登录](common/tutorial_general_301.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![配置单一登录](common/editconfigure.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![FreshDesk 域和 URL 单一登录信息](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<tenant-name>.freshdesk.com`，或者键入 Freshdesk 建议的其他值。

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<tenant-name>.freshdesk.com`，或者键入 Freshdesk 建议的其他值。

    > [!NOTE]
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 若要获取这些值，请与 [FreshDesk 客户端支持团队](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg)联系。

5. FreshDesk 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示一个示例。 “用户标识符”的默认值是“user.userprincipalname”，但 **FreshDesk** 要求通过用户的电子邮件地址映射此项。 为此，可以使用列表中的 **user.mail** 属性，或使用基于组织配置的相应属性值。

    ![图像](./media/freshdesk-tutorial/i4-attribute.png)

6. 在“用户属性和声明”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    a. 单击“编辑”图标，以打开“管理用户声明”对话框。

    ![图像](./media/freshdesk-tutorial/i2-attribute.png)

    ![图像](./media/freshdesk-tutorial/i3-attribute.png)

    b. 在“源属性”列表中，选择 **user.mail**。

    c. 单击“ **保存**”。

7. 在“SAML 签名证书”页的“SAML 签名证书”部分中，单击“下载”以下载“证书(Base64)”并将证书文件保存在计算机上。

    ![证书下载链接](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

8. 打开**命令提示符**并运行以下命令：

    a. 在命令提示符中输入 `certutil.exe -dump FreshDesk.cer` 值。

    > [!NOTE]
    > 此处，**FreshDesk.cer** 是从 Azure 门户下载的证书。

    b. 复制**证书哈希(sha256)** 值并将其粘贴到记事本中。 

9. 在“设置 FreshDesk”部分中，根据你的要求复制相应的 URL。

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    ![FreshDesk 配置](common/configuresection.png)

10. 在另一个 Web 浏览器窗口中，以管理员身份登录 Freshdesk 公司站点。

11. 选择**设置图标**，在“安全性”部分中，执行以下步骤：

    ![单一登录](./media/freshdesk-tutorial/IC776770.png "单一登录")
  
    a. 对于“单一登录(SSO)”，请选择“启用”。

    b. 选择“SAML SSO”。

    c. 在“SAML 登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    d. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    e. 在“安全证书指纹”文本框中，粘贴你之前获得的**证书哈希(sha256)** 值。
  
    f. 单击“ **保存**”。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](common/create_aaduser_01.png) 

3. 在“用户属性”中，按照以下步骤操作。

    ![创建 Azure AD 测试用户](common/create_aaduser_02.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="creating-a-freshdesk-test-user"></a>创建 FreshDesk 测试用户

为使 Azure AD 用户能够登录到 FreshDesk，必须将其预配到 FreshDesk。  
就 FreshDesk 而言，预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到“Freshdesk”租户。

2. 在顶部菜单中，单击“管理员”。

    ![管理员](./media/freshdesk-tutorial/IC776772.png "管理员")

3. 在“常规设置”选项卡上，单击“代理”。
  
    ![代理](./media/freshdesk-tutorial/IC776773.png "Agents")

4. 单击“新建代理”。

    ![新建代理](./media/freshdesk-tutorial/IC776774.png "New Agent")

5. 在“代理信息”对话框中，执行以下步骤：

    ![代理信息](./media/freshdesk-tutorial/IC776775.png "Agent Information")

    a. 在“电子邮件”文本框中，键入要预配的 Azure AD 帐户的 Azure AD 电子邮件地址。

    b. 在“完整名称”文本框中，键入要预配的 Azure AD 帐户的名称。

    c. 在“名称”文本框中，键入要预配的 Azure AD 帐户的名称。

    d. 单击“ **保存**”。

    >[!NOTE]
    >Azure AD 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
    >
    >[!NOTE]
    >可以使用任何其他 Freshdesk 用户帐户创建工具或 Freshdesk 提供的 API 来将 AAD 用户帐户预配到 FreshDesk。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予对 FreshDesk 的访问权限使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“FreshDesk”。

    ![配置单一登录](./media/freshdesk-tutorial/tutorial_freshdesk_app.png)

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 FreshDesk 磁贴时，应当会自动登录到 FreshDesk 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png

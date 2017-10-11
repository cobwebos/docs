---
title: "教程： Azure Active Directory 集成 EthicsPoint 事件管理 (EPIM) |Microsoft 文档"
description: "了解如何配置单一登录 Azure Active Directory 和 EthicsPoint 事件管理 (EPIM) 之间。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 8cb31a4c-9309-469b-93ac-daf0d3c7a3e6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: b5ac3afd973b5765ba151e766754934b49ac0e0c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ethicspoint-incident-management-epim"></a>教程： Azure Active Directory 集成 EthicsPoint 事件管理 (EPIM)

在本教程中，你将学习如何与 Azure Active Directory (Azure AD) 集成 EthicsPoint 事件管理 (EPIM)。

与 Azure AD 集成 EthicsPoint 事件管理 (EPIM) 提供了以下好处：

- 你可以控制有权 EthicsPoint 事件管理 (EPIM) 到 Azure AD 中
- 你可以使用其 Azure AD 帐户使用户可以自动获取登录到 EthicsPoint 事件管理 (EPIM) （单一登录）
- 你可以管理你的帐户在一个中心位置-Azure 门户

如果你想要了解有关与 Azure AD 的 SaaS 应用程序集成的详细信息，请参阅[应用程序访问与单一登录与 Azure Active Directory 是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 集成 EthicsPoint 事件管理 (EPIM)，你需要以下各项：

- Azure AD 订阅
- EthicsPoint 事件管理 (EPIM) 上单一登录启用的订阅

> [!NOTE]
> 若要测试步骤在本教程中，我们不建议使用生产环境。

若要测试本教程中的步骤，你应遵循这些建议：

- 除非必要，否则，不要使用你的生产环境。
- 如果你没有 Azure AD 试用环境，你可以获取一个月试用版[此处](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案说明
在本教程中，你测试 Azure AD 单一登录在测试环境。 在本教程中概述的方案由两个主要构建基块组成：

1. 从库添加 EthicsPoint 事件管理 (EPIM)
2. 配置和测试 Azure AD 单一登录

## <a name="adding-ethicspoint-incident-management-epim-from-the-gallery"></a>从库添加 EthicsPoint 事件管理 (EPIM)
若要配置集成的 EthicsPoint 事件管理 (EPIM) 到 Azure AD，你需要从库中 EthicsPoint 事件管理 (EPIM) 添加到托管的 SaaS 应用程序列表。

**若要从库添加 EthicsPoint 事件管理 (EPIM)，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**，在左的导航面板中，单击**Azure Active Directory**图标。 

    ![Active Directory][1]

2. 导航到**企业应用程序**。 然后转到**所有应用程序**。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击**新的应用程序**对话框顶部的按钮。

    ![应用程序][3]

4. 在搜索框中，键入**EthicsPoint 事件管理 (EPIM)**。

    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspoint_search.png)

5. 在结果面板中，选择**EthicsPoint 事件管理 (EPIM)**，然后单击**添加**按钮以添加该应用程序。

    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspoint_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，可以配置和测试 Azure AD 单一登录与 EthicsPoint 事件管理 (EPIM) 基于一个名为"Britta 人 Simon。"的测试用户

对于单一登录工作，Azure AD 需要知道所对应用户 EthicsPoint 事件管理 (EPIM) 中的用户在 Azure AD 中。 换而言之，Azure AD 用户和相关的用户在 EthicsPoint 事件管理 (EPIM) 之间的链接关系需要建立。

在 EthicsPoint 事件管理 (EPIM) 分配的值**用户名**作为的值的 Azure AD 中**用户名**建立的链接关系。

若要配置和测试 Azure AD 单一登录与 EthicsPoint 事件管理 (EPIM)，你需要完成以下构建基块：

1. **[Azure AD 单一登录配置](#configuring-azure-ad-single-sign-on)** -若要使用户可以使用此功能。
2. **[创建一个 Azure AD 的测试用户](#creating-an-azure-ad-test-user)** -若要测试 Azure AD 单一登录与 Britta 人 Simon。
3. **[创建 EthicsPoint 事件管理 (EPIM) 测试用户](#creating-a-ethicspoint-incident-management-epim-test-user)** -若要在 EthicsPoint 事件管理 (EPIM) 链接到用户的 Azure AD 表示具有 Britta 人 Simon 的副本。
4. **[Azure AD 测试用户分配](#assigning-the-azure-ad-test-user)** -若要启用 Britta 人 Simon 用于 Azure AD 单一登录。
5. **[单一登录测试](#testing-single-sign-on)** -若要验证是否配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，可以启用 Azure AD 单一登录在 Azure 门户和 EthicsPoint 事件管理 (EPIM) 应用程序中配置单一登录。

**若要配置 Azure AD 单一登录与 EthicsPoint 事件管理 (EPIM)，请执行以下步骤：**

1. 在 Azure 门户中，在**EthicsPoint 事件管理 (EPIM)**应用程序集成页上，单击**单一登录**。

    ![配置单一登录][4]

2. 上**单一登录**对话框中，选择**模式**作为**SAML 基于登录**若要启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspoint_samlbase.png)

3. 上**EthicsPoint 事件管理 (EPIM) 域和 Url**部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspoint_url.png)

    a. 在**登录 URL**文本框中，键入 URL 使用以下模式：
    | |
    |--|
    | `https://<companyname>.navexglobal.com`|
    | `https://<companyname>.ethicspointvp.com`|

    b。 在**标识符**文本框中，键入 URL 使用以下模式：`https://<companyname>.navexglobal.com/adfs/services/trust`

    c. 在**答复 URL**文本框中，键入 URL 使用以下模式：`https://<servername>.navexglobal.com/adfs/ls/`

    > [!NOTE] 
    > 这些值不是实际的。 使用实际的答复 URL、 标识符和登录 URL 更新这些值。 联系人[EthicsPoint 事件管理 (EPIM) 客户端支持团队](http://www.navexglobal.com/company/contact-us)以获取这些值。 

4. 上**SAML 签名证书**部分中，单击**元数据 XML**然后将保存您的计算机上的元数据文件。

    ![配置单一登录](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspoint_certificate.png) 

5. 单击**保存**按钮。

    ![配置单一登录](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_400.png)
    
6. 在上配置单一登录**EthicsPoint 事件管理 (EPIM)**端，你需要发送下载**元数据 XML**到[EthicsPoint 事件管理 (EPIM) 支持团队](http://www.navexglobal.com/company/contact-us)。

> [!TIP]
> 你现在可以读取内部这些指令的简洁版本[Azure 门户](https://portal.azure.com)，而你要设置应用程序 ！  在添加此应用程序从后**Active Directory > 企业应用程序**部分中，只需单击**上单一登录**选项卡上和访问通过的嵌入的文档**配置**底部部分中的。 你可以阅读更多有关嵌入的文档的功能： [Azure AD 嵌入文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建一个 Azure AD 的测试用户
此部分的目的是在调用 Britta 人 Simon Azure 门户中创建一个测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建一个测试用户，请执行以下步骤：**

1. 在**Azure 门户**，左侧的导航窗格中，单击**Azure Active Directory**图标。

    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_01.png) 

2. 若要显示的用户的列表，请转到**用户和组**单击**所有用户**。
    
    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_02.png) 

3. 若要打开**用户**对话框中，单击**添加**对话框顶部。
 
    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_03.png) 

4. 上**用户**对话框页上，执行以下步骤：
 
    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_04.png) 

    a. 在**名称**文本框中，键入**BrittaSimon**。

    b。 在**用户名**文本框中，键入**电子邮件地址**BrittaSimon。

    c. 选择**显示密码**并记下的值**密码**。

    d. 单击“创建”。
 
### <a name="creating-a-ethicspoint-incident-management-epim-test-user"></a>创建 EthicsPoint 事件管理 (EPIM) 测试用户

在本部分中，你将创建一个名 Britta 人 Simon EthicsPoint 事件管理 (EPIM) 中的用户。 请使用[EthicsPoint 事件管理 (EPIM) 支持团队](http://www.navexglobal.com/company/contact-us)可以 EthicsPoint 事件管理 (EPIM) 平台中添加的用户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在此部分中，你将启用 Britta 人 Simon 用于通过到 EthicsPoint 事件管理 (EPIM) 授予访问 Azure 单一登录。

![将用户分配][200] 

**若要将 Britta 人 Simon 分配到 EthicsPoint 事件管理 (EPIM)，请执行以下步骤：**

1. 在 Azure 门户中，打开应用程序视图，然后导航到的目录视图，并转到**企业应用程序**然后单击**所有应用程序**。

    ![将用户分配][201] 

2. 在应用程序列表中，选择**EthicsPoint 事件管理 (EPIM)**。

    ![配置单一登录](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspoint_app.png) 

3. 在左侧菜单中，单击**用户和组**。

    ![将用户分配][202] 

4. 单击**添加**按钮。 然后选择**用户和组**上**添加分配**对话框。

    ![将用户分配][203]

5. 上**用户和组**对话框中，选择**Britta 人 Simon**用户列表中。

6. 单击**选择**按钮上**用户和组**对话框。

7. 单击**分配**按钮上**添加分配**对话框。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在此部分中，你将测试 Azure AD 单一登录配置使用访问面板。
当你单击访问面板中的 EthicsPoint 事件管理 (EPIM) 磁贴时，你应该获取自动登录到你 EthicsPoint 事件管理 (EPIM) 的应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何与 Azure Active Directory 集成 SaaS 应用程序教程列表](active-directory-saas-tutorial-list.md)
* [应用程序访问与单一登录与 Azure Active Directory 是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_203.png


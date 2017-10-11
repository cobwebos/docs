---
title: "教程： Azure Active Directory 与 Clarizen 的集成 |Microsoft 文档"
description: "了解如何配置单一登录 Azure Active Directory 与 Clarizen 之间。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: 574c6877bddac8be7d6d541bfabbdc10f6be3101
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>教程： Azure Active Directory 与 Clarizen 的集成

在本教程中，与 Clarizen 学习如何将 Azure Active Directory (Azure AD) 集成。 此集成为你提供了以下好处：

- 你可以控制，Azure AD 中，有权访问 Clarizen。
- 你可以启用你的用户可自动登录到 Clarizen （上单一登录） 使用其 Azure AD 帐户。
- 你可以管理你在一个中心位置，Azure 门户的帐户。

在本教程的方案由两个主要任务组成：

1. 从库中添加 Clarizen。
2. 配置并测试 Azure AD 单一登录。

如果你需要有关软件的更多详细信息作为与 Azure AD 服务 (SaaS) 应用程序集成，请参阅[应用程序访问与单一登录与 Azure Active Directory 是什么？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必备条件
若要配置与 Clarizen 的 Azure AD 集成，你需要以下各项：

- Azure AD 订阅
- 为单一登录启用 Clarizen 订阅

若要测试本教程中的步骤，请遵循这些建议：

- 在测试环境中测试 Azure AD 单一登录。 不要使用生产环境中，除非这是必需的。
- 如果你没有 Azure AD 测试环境，则可以[获取一个月试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="add-clarizen-from-the-gallery"></a>从库中添加 Clarizen
若要配置到 Azure AD Clarizen 的集成，请将 Clarizen 从库添加到托管的 SaaS 应用程序列表上。

1. 在[Azure 门户](https://portal.azure.com)，在左窗格中，单击**Azure Active Directory**图标。

    ![Azure Active Directory 图标][1]

2. 单击**企业应用程序**。 然后单击**所有应用程序**。

    ![单击"企业应用程序"和"所有应用程序"][2]

3. 单击**添加**对话框顶部的按钮。

    !["添加"按钮][3]

4. 在搜索框中，键入**Clarizen**。

    ![在搜索框中键入"Clarizen"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_000.png)

5. 在结果窗格中，选择**Clarizen**，然后单击**添加**以添加该应用程序。

    ![在结果窗格中选择 Clarizen](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置并测试 Azure AD 单一登录
在以下部分中，可以配置和测试 Azure AD 单一登录与 Clarizen 根据测试的用户 Britta 人 Simon。

对于单一登录工作，Azure AD 需要知道什么对应项中的用户 Clarizen 是向用户在 Azure AD 中。 换而言之，Azure AD 用户和 Clarizen 中的相关的用户之间的链接关系需要建立。 通过分配的值建立此链接关系**用户名**作为的值的 Azure AD 中**用户名**Clarizen 中。

若要配置并测试 Azure AD 单一登录与 Clarizen，请完成以下构建基块：

1. **[Azure AD 单一登录配置](#configure-azure-ad-single-sign-on)**以便用户可以使用此功能。
2. **[创建 Azure AD 的测试用户](#create-an-azure-ad-test-user)** Azure AD 单一登录与 Britta 人 Simon 测试。
3. **[创建一个 Clarizen 测试用户](#create-a-clarizen-test-user)**能够 Britta 人 Simon 对应项中链接到的 Azure AD 表示形式她的 Clarizen。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)**启用 Britta 人 Simon 用于 Azure AD 单一登录。
5. **[单一登录测试](#test-single-sign-on)**以验证配置是否适用。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
Azure AD 单一登录 Azure 门户中启用和配置单一登录 Clarizen 应用程序中。

1. 在 Azure 门户中，在**Clarizen**应用程序集成页上，单击**单一登录**。

    ![单击"上单一登录"][4]

2. 在**单一登录**对话框中，为**模式**，选择**SAML 基于登录**若要启用单一登录。

    ![选择"基于 SAML 登录"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_01.png)

3. 在**Clarizen 域和 Url**部分中，执行以下步骤：

    ![标识符和答复 URL 框](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_02.png)

    a. 在**标识符**框中，键入形式的值： **Clarizen**

    b。 在**答复 URL**框中，通过使用以下模式键入 URL: **https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx**

    > [!NOTE]
    > 这些不是实际值。 你必须使用的实际标识符和答复 URL。 此处我们建议你使用的唯一值的字符串作为标识符。 若要获取的实际值，请联系[Clarizen 支持团队](https://success.clarizen.com/hc/en-us/requests/new)。

4. 上**SAML 签名证书**部分中，单击**创建新证书**。

    ![单击"创建新证书"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_03.png)  

5. 在**创建新证书**对话框中，单击日历图标并选择一个到期日期。 然后，单击“**保存**”。

    ![选择并保存一个到期日期](./media/active-directory-saas-clarizen-tutorial/tutorial_general_300.png)

6. 在**SAML 签名证书**部分中，选择**激活新证书**，然后单击**保存**。

    ![选中的复选框来激活新证书](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_04.png)

7. 在**滚动更新证书**对话框中，单击**确定**。

    ![单击"确定"以确认你想要使证书处于活动状态](./media/active-directory-saas-clarizen-tutorial/tutorial_general_400.png)

8. 在**SAML 签名证书**部分中，单击**证书 (Base64)**然后将保存您的计算机上的证书文件。

    ![单击"证书 (Base64)"以开始下载](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_05.png)

9. 在**Clarizen 配置**部分中，单击**配置 Clarizen**以打开**上配置登录**窗口。

    ![单击"配置 Clarizen"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_06.png)

    !["登录配置"窗口中，其中包括文件和 Url](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_07.png)

10. 在另一个 web 浏览器窗口中，登录到你的 Clarizen 公司站点以管理员身份。

11. 单击你的用户名，然后单击**设置**。

    ![在你的用户名下单击"设置"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_001.png "设置")

12. 单击**全局设置**选项卡。 然后，旁边**联合身份验证**，单击**编辑**。

    !["全局设置"选项卡](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_002.png "全局设置")

13. 在**联合身份验证**对话框框中，执行以下步骤：

    !["联合身份验证"对话框中](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_003.png "联合身份验证")

    a. 选择**启用联合身份验证**。

    b。 单击**上载**上载你下载的证书。

    c. 在**登录 URL**框中，输入的值**SAML 单一登录服务 URL**从 Azure AD 应用程序配置窗口。

    d. 在**注销 URL**框中，输入的值**注销 URL**从 Azure AD 应用程序配置窗口。

    e. 选择**使用 POST**。

    f. 单击 **“保存”**。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 的测试用户
在 Azure 门户中，创建一个名为 Britta 人 Simon 的测试用户。

![Azure AD 测试用户的名称和电子邮件地址][100]

1. 在 Azure 门户中，在左窗格中，单击**Azure Active Directory**图标。

    ![Azure Active Directory 图标](./media/active-directory-saas-clarizen-tutorial/create_aaduser_01.png)

2. 单击**用户和组**，然后单击**所有用户**以显示用户的列表。

    ![单击"用户和组"和"所有用户"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_02.png)

3. 在对话框中的顶部，单击**添加**以打开**用户**对话框。

    !["添加"按钮](./media/active-directory-saas-clarizen-tutorial/create_aaduser_03.png)

4. 在**用户**对话框框中，执行以下步骤：

    ![使用名称、 电子邮件地址和密码的"用户"对话框中填写](./media/active-directory-saas-clarizen-tutorial/create_aaduser_04.png)

    a. 在**名称**框中，键入**BrittaSimon**。

    b。 在**用户名**框中，键入 Britta 人 Simon 帐户的电子邮件地址。

    c. 选择**显示密码**并记下的值**密码**。

    d. 单击“创建”。

### <a name="create-a-clarizen-test-user"></a>创建一个 Clarizen 测试用户
若要启用 Azure AD 用户能够登录到 Clarizen，必须设置用户帐户。 就 Clarizen 而言，设置是一项手动任务。

1. 以管理员身份登录到你的 Clarizen 公司站点。

2. 单击**人员**。

    ![单击"People"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_001.png "人员")

3. 单击**邀请用户**。

    !["邀请用户"按钮](./media/active-directory-saas-clarizen-tutorial/create_aaduser_002.png "邀请用户")

4. 在**邀请他人**对话框框中，执行以下步骤：

    !["邀请他人"对话框中](./media/active-directory-saas-clarizen-tutorial/create_aaduser_003.png "邀请他人")

    a. 在**电子邮件**框中，键入 Britta 人 Simon 帐户的电子邮件地址。

    b。 单击**邀请**。

    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，并按照要激活前确认其帐户的链接。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
启用 Britta 人 Simon 用于通过到 Clarizen 授予其访问 Azure 单一登录。

![分配的测试用户][200]

1. 在 Azure 门户中，打开应用程序查看，请浏览到目录视图中，单击**企业应用程序**，然后单击**所有应用程序**。

    ![单击"企业应用程序"和"所有应用程序"][201]

2. 在应用程序列表中，选择**Clarizen**。

    ![在列表中选择 Clarizen](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_50.png)

3. 在左窗格中，单击**用户和组**。

    ![单击"用户和组"][202]

4. 单击**添加**按钮。 然后，在**添加分配**对话框中，选择**用户和组**。

    !["添加"按钮和"添加任务"对话框中][203]

5. 在**用户和组**对话框中，选择**Britta 人 Simon**中的用户的列表。

6. 在**用户和组**对话框中，单击**选择**按钮。

7. 在**添加分配**对话框中，单击**分配**按钮。

### <a name="test-single-sign-on"></a>测试单一登录
通过使用访问面板测试 Azure AD 单一登录配置。

当你单击访问面板中的 Clarizen 磁贴时，你应会自动登录到 Clarizen 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何与 Azure Active Directory 集成 SaaS 应用程序教程列表](active-directory-saas-tutorial-list.md)
* [应用程序访问与单一登录与 Azure Active Directory 是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_203.png

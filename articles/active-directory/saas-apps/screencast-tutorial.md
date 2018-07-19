---
title: 教程：Azure Active Directory 与 Screencast-O-Matic 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Screencast-O-Matic 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeedes
ms.openlocfilehash: 20c0acebde232bd50e6e5befed0facc96ee11b4d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040943"
---
# <a name="tutorial-azure-active-directory-integration-with-screencast-o-matic"></a>教程：Azure Active Directory 与 Screencast-O-Matic 集成

本教程介绍如何将 Screencast-O-Matic 与 Azure Active Directory (Azure AD) 集成。

将 Screencast-O-Matic 与 Azure AD 集成具有以下优势：

- 可以在 Azure AD 中控制谁有权限访问 Screencast-O-Matic。
- 可以让用户使用其 Azure AD 帐户自动登录到 Screencast-O-Matic（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Screencast-O-Matic 的集成，需备齐以下项目：

- Azure AD 订阅
- 启用了 Screencast-O-Matic 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Screencast-O-Matic
2. 配置和测试 Azure AD 单一登录

## <a name="adding-screencast-o-matic-from-the-gallery"></a>从库中添加 Screencast-O-Matic
若要配置 Screencast-O-Matic 与 Azure AD 的集成，需要从库中将 Screencast-O-Matic 添加到托管 SaaS 应用列表。

**若要从库中添加 Screencast-O-Matic，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“Screencast-O-Matic”，在结果面板中选择“Screencast-O-Matic”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Screencast-O-Matic](./media/screencast-tutorial/tutorial_screencast_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，请根据名为“Britta Simon”的测试用户的情况配置和测试 Screencast-O-Matic 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Screencast-O-Matic 用户。 换句话说，需要建立 Azure AD 用户与 Screencast-O-Matic 中相关用户之间的关联关系。

若要配置和测试 Screencast-O-Matic 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Screencast-O-Matic 测试用户](#create-a-screencast-o-matic-test-user)** - 在 Screencast-O-Matic 中创建 Britta Simon 的对应用户，并将其关联到用户的 Azure AD 身份。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，请在 Azure 门户中启用 Azure AD 单一登录并在 Screencast-O-Matic 应用程序中配置单一登录。

**若要配置 Screencast-O-Matic 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 Screencast-O-Matic 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/screencast-tutorial/tutorial_screencast_samlbase.png)

3. 在“Screencast-O-Matic 域和 URL”部分中，执行以下步骤：

    ![Screencast-O-Matic 域和 URL 单一登录信息](./media/screencast-tutorial/tutorial_screencast_url.png)

    在“登录 URL”文本框中，使用以下模式键入 URL： `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE] 
    > 登录 URL 值不是实际值。 请使用实际登录 URL 更新此值。 请联系 [Screencast-O-Matic 客户端支持团队](mailto:support@screencast-o-matic.com)以获取此值。 
 
4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/screencast-tutorial/tutorial_screencast_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/screencast-tutorial/tutorial_general_400.png)

6. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Screencast-O-Matic。

7. 单击“订阅”。

    ![订阅](./media/screencast-tutorial/tutorial_screencast_sub.png)

8. 在“访问页”部分，单击“设置”。

    ![访问](./media/screencast-tutorial/tutorial_screencast_setup.png)

9. 在“设置访问页”上，执行以下步骤：

    * 在“访问 URL”部分的指定文本框中，键入实例名。

    ![访问](./media/screencast-tutorial/tutorial_screencast_access.png)

    * 在“SAML 用户限制(可选)”部分选择“需要域用户”。

    * 在“上传 IDP 元数据 XML 文件”下单击“选择文件”，以便上传已从 Azure 门户下载的元数据。

    * 单击“确定”。 

    ![访问](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/screencast-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/screencast-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/screencast-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/screencast-tutorial/create_aaduser_04.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-screencast-o-matic-test-user"></a>创建 Screencast-O-Matic 测试用户

本部分的目的是在 Screencast-O-Matic 中创建名为“Britta Simon”的用户。 Screencast-O-Matic 支持默认启用的恰时预配。 此部分不存在任何操作项。 尝试访问 Screencast-O-Matic 期间，如果尚不存在用户，则会创建一个新用户。

>[!Note]
>如果需要手动创建用户，请联系 [Screencast-O-Matic 客户端支持团队](mailto:support@screencast-o-matic.com)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Screencast-O-Matic 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Screencast-O-Matic，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Screencast-O-Matic”。

    ![应用程序列表中的 Screencast-O-Matic 链接](./media/screencast-tutorial/tutorial_screencast_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Screencast-O-Matic”磁贴时，会自动登录到 Screencast-O-Matic 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/screencast-tutorial/tutorial_general_01.png
[2]: ./media/screencast-tutorial/tutorial_general_02.png
[3]: ./media/screencast-tutorial/tutorial_general_03.png
[4]: ./media/screencast-tutorial/tutorial_general_04.png

[100]: ./media/screencast-tutorial/tutorial_general_100.png

[200]: ./media/screencast-tutorial/tutorial_general_200.png
[201]: ./media/screencast-tutorial/tutorial_general_201.png
[202]: ./media/screencast-tutorial/tutorial_general_202.png
[203]: ./media/screencast-tutorial/tutorial_general_203.png


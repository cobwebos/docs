---
title: 教程：Azure Active Directory 与 O.C. Tanner - AppreciateHub | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 O.C. Tanner - AppreciateHub 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.openlocfilehash: 29f3acc5992502f87fa57156f12244cb074f0491
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210775"
---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>教程：Azure Active Directory 与 O.C. Tanner - AppreciateHub

本教程介绍了如何将 O.C. Tanner - AppreciateHub 与 Azure Active Directory (Azure AD) 集成。

将 O.C. Tanner - AppreciateHub 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 O.C. Tanner - AppreciateHub
- 可以让用户通过其 Azure AD 帐户自动登录到 O.C. Tanner - AppreciateHub（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 O.C. Tanner - AppreciateHub 的集成，需要备齐以下项目：

- Azure AD 订阅
- 启用了 O.C. 已启用 Tanner - AppreciateHub 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 O.C. Tanner - AppreciateHub
2. 配置和测试 Azure AD 单一登录

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>从库添加 O.C. Tanner - AppreciateHub
要通过配置将 O.C. Tanner - AppreciateHub 集成到 Azure AD 中，需要从库将 O.C. Tanner - AppreciateHub 添加到托管式 SaaS 应用的列表中。

**若要从库添加 O.C.Tanner - AppreciateHub，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“O.C. Tanner - AppreciateHub”。**

    ![创建 Azure AD 测试用户](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_search.png)

5. 在结果面板中，选择“O.C. Tanner - AppreciateHub”****，然后单击“添加”** 按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将配置和测试 O.C.  Tanner - AppreciateHub 的 Azure AD 单一登录。

若要使用单一登录，Azure AD 需要了解与 Azure AD 中的用户相对应的 O.C. Tanner - AppreciateHub 中的用户。 换句话说，需要建立 Azure AD 用户与 O.C. Tanner - AppreciateHub 中相关用户之间的关联关系。

可通过将 Azure AD 中“用户名”的值指定为 O.C.Tanner - AppreciateHub 中“用户名”的值来建立此关联关系。

若要使用 O.C. Tanner - AppreciateHub 配置和测试 Azure AD 单一登录，需完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 O.C.Tanner - AppreciateHub 测试用户](#creating-a-oc-tanner---appreciatehub-test-user)** - 目的是在 O.C. Tanner - AppreciateHub 中有一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 O.C. Tanner - AppreciateHub 应用程序中配置单一登录。

**若要通过 O.C.Tanner - AppreciateHub 中创建名为“Britta Simon”的用户，请执行以下步骤：**

1. 在 Azure 门户中的**O.C. Tanner-AppreciateHub”应用程序集成页上，单击“单一登录”**。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_samlbase.png)

3. 在**O.C. Tanner - AppreciateHub 域和 URL”部分中，执行以下步骤：**

    ![配置单一登录](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_url.png)

    a. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.octanner.net/sp/ACS.saml2`

    > [!NOTE] 
    > 此值不是真实值。 请使用实际回复 URL 更新此值。 请联系 [O.C.Tanner - AppreciateHub 支持团队](mailto:sso@octanner.com)获取此值。

    b. 使用以下链接打开元数据文件：[https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata)。
   
    c. 找到 **md:AssertionConsumerService** 节点。 
   
    d. 复制 **Location** 属性的值。 
   
    ![配置应用设置][12]
   
    e. 在“登录 URL”文本框中，传递在上一步获得的值。

4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![配置单一登录](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/oc-tanner-tutorial/tutorial_general_400.png)

6. 要在 O.C.Tanner-AppreciateHub** 端配置单一登录，需要将下载的元数据 XML 发送到 [O.C.Tanner - AppreciateHub 支持团队](mailto:sso@octanner.com)。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/oc-tanner-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/oc-tanner-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/oc-tanner-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/oc-tanner-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>创建 O.C. Tanner - AppreciateHub 测试用户

本部分的目的是在 O.C. Tanner - AppreciateHub 中创建名为“Britta Simon”的用户。

**若要在 O.C.Tanner - AppreciateHub 中创建名为“Britta Simon”的用户，请执行以下步骤：**

要求 [O.C. Tanner - AppreciateHub 支持团队](mailto:sso@octanner.com)创建一个用户，该用户的 nameID 属性值与 Azure AD 中 Britta Simon 的用户名相同。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 O.C. Tanner - AppreciateHub. 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 O.C.Tanner - AppreciateHub 中创建名为“Britta Simon”的用户，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“**O.C.Tanner - AppreciateHub**”。

    ![配置单一登录](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD 单一登录配置。  
在访问面板中单击“O.C. Tanner - AppreciateHub”磁贴时，用户会自动登录到 O.C. Tanner - AppreciateHub 应用程序中配置单一登录。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/oc-tanner-tutorial/tutorial_general_04.png

[12]: ./media/oc-tanner-tutorial/tutorial_octanner_08.png

[100]: ./media/oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/oc-tanner-tutorial/tutorial_general_202.png
[203]: ./media/oc-tanner-tutorial/tutorial_general_203.png


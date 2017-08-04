---
title: "教程：Azure Active Directory 与 ArcGIS Online 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 ArcGIS Online 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: df72270ca6443b456c079b22425f1660aa522389
ms.contentlocale: zh-cn
ms.lasthandoff: 06/16/2017


---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>教程：Azure Active Directory 与 ArcGIS Online 集成

在本教程中，了解如何将 ArcGIS Online 与 Azure Active Directory (Azure AD) 集成。

将 ArcGIS Online 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权限访问 ArcGIS Online
- 可使用户通过其 Azure AD 帐户自动登录 ArcGIS Online（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

<!--## Overview

To enable single sign-on with ArcGIS Online, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in ArcGIS Online.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 ArcGIS Online 的集成，需要具有以下项：

- 一个 Azure AD 订阅
- 已启用 ArcGIS Online 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 ArcGIS Online
2. 配置和测试 Azure AD 单一登录

## <a name="adding-arcgis-online-from-the-gallery"></a>从库中添加 ArcGIS Online
若要配置 ArcGIS Online 与 Azure AD 的集成，需要将库中的 ArcGIS Online 添加到托管的 SaaS 应用列表。

若要从库中添加 ArcGIS Online，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“新建应用程序”按钮以添加新的应用程序。

    ![应用程序][3]

4. 在搜索框中，键入 ArcGIS Online。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_search.png)

5. 在“结果”窗格中，选择“ArcGIS Online”，然后单击“添加”按钮，添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 ArcGIS Online 的 Azure AD 单一登录。

若要使用单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 ArcGIS Online 用户。 换句话说，需要在 Azure AD 用户与 ArcGIS Online 中相关用户之间建立链接关系。

通过将 Azure AD 中“用户名”的值指定为 ArcGIS Online 中“用户名”的值来建立此链接关系。

若要配置和测试 ArcGIS Online 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 ArcGIS Online 测试用户](#creating-an-arcgis-online-test-user)** - 在 ArcGIS Online 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将介绍如何在 Azure 门户中启用 Azure AD 单一登录，并在 ArcGIS Online 应用程序中配置单一登录。

若要配置 ArcGIS Online 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的“ArcGIS Online”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_samlbase.png)

3. 在“ArcGIS Online 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_url.png)

    在“登录 URL”文本框中，使用以下模式键入值：`https://<company>.maps.arcgis.com`

    > [!NOTE] 
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 若要获取此值，请与 [ArcGIS Online 客户端支持团队](http://support.esri.com/)联系。 

4. 在“SAML 签名证书”部分中，单击“元数据 XML”，然后在计算机上保存 XML 文件。

    ![配置单一登录](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-arcgis-tutorial/tutorial_general_400.png)

6. 在其他 Web 浏览器窗口中，以管理员身份登录 ArcGIS 公司站点。

7. 单击“编辑设置”。

    ![编辑设置](./media/active-directory-saas-arcgis-tutorial/ic784742.png "编辑设置")

8. 单击“安全”。

    ![安全](./media/active-directory-saas-arcgis-tutorial/ic784743.png "安全")

9. 在“企业登录”下，单击“设置标识提供者”。

    ![企业登录名](./media/active-directory-saas-arcgis-tutorial/ic784744.png "企业登录名")

10. 在“设置标识提供者”配置页上，执行以下步骤：
   
    ![设置标识提供者](./media/active-directory-saas-arcgis-tutorial/ic784745.png "设置标识提供者")
   
    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“名称”文本框中，键入组织的名称。

    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 对于“企业标识提供者提供元数据所使用的方法”，请选择“文件”。

    c. 若要上传下载的元数据文件，请单击“选择文件”。

    d. 单击“设置标识提供者”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-arcgis-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-arcgis-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-arcgis-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-arcgis-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 Britta Simon 的**电子邮件地址**。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-an-arcgis-online-test-user"></a>创建 ArcGIS Online 测试用户

为了使 Azure AD 用户能够登录 ArcGIS Online，必须对其进行预配才能登录 ArcGIS Online。  
就 ArcGIS Online 来说，预配任务需要手动完成。

**若要预配用户帐户，请执行以下步骤：**

1. 登录“ArcGIS”租户。

2. 单击“邀请成员”。
   
    ![邀请成员](./media/active-directory-saas-arcgis-tutorial/ic784747.png "邀请成员")

3. 选择“自动添加成员且无需发送电子邮件”，然后单击“下一步”。
   
    ![自动添加成员](./media/active-directory-saas-arcgis-tutorial/ic784748.png "自动添加成员")

4. 在“成员”对话框页上，执行以下步骤：
   
     ![添加和查看](./media/active-directory-saas-arcgis-tutorial/ic784749.png "添加和查看")
    
     a.在“横幅徽标”下面，选择“删除上传的徽标”。 输入想要预配的有效 AAD 帐户的“电子邮件”、“名字”和“姓氏”。
  
     b.在“磁贴徽标”下面，选择“删除上传的徽标”。 单击“添加和查看”。
5. 查看输入的数据，然后单击“添加成员”。
   
    ![添加成员](./media/active-directory-saas-arcgis-tutorial/ic784750.png "添加成员")
        
    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，并且将单击其中的链接以在激活帐户前确认帐户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 ArcGIS Online 的权限，支持其使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 ArcGIS Online，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“ArcGIS Online”。

    ![配置单一登录](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击“ArcGIS Online”磁贴时，应该会自动登录“ArcGIS Online”应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_203.png



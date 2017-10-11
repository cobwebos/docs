---
title: "教程：Azure Active Directory 与 Amazon Web Services (AWS) 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Amazon Web Services (AWS) 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 0fb9c8f428368271b548e3f174726fa01ea910c5
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>教程：Azure Active Directory 与 Amazon Web Services (AWS) 集成

本教程介绍了如何将 Amazon Web Services (AWS) 与 Azure Active Directory (Azure AD) 进行集成。

将 Amazon Web Services (AWS) 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Amazon Web Services (AWS)
- 可以让用户使用其 Azure AD 帐户自动登录到 Amazon Web Services (AWS)（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

<!--## Overview

To enable single sign-on with Amazon Web Services (AWS), it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Amazon Web Services (AWS).

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Amazon Web Services (AWS) 的集成，需要具有以下项：

- 一个 Azure AD 订阅
- 启用了 Amazon Web Services (AWS) 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Amazon Web Services (AWS)
2. 配置和测试 Azure AD 单一登录

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>从库中添加 Amazon Web Services (AWS)
要配置 Amazon Web Services (AWS) 与 Azure AD 的集成，需要从库将 Amazon Web Services (AWS) 添加到托管 SaaS 应用列表。

**若要从库中添加 Amazon Web Services (AWS)，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 **Amazon Web Services (AWS)**。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_search.png)

5. 在结果窗格中，选择“Amazon Web Services (AWS)”，并单击“添加”以添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Amazon Web Services (AWS) 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Amazon Web Services (AWS) 用户。 换句话说，需在 Azure AD 用户与 Amazon Web Services (AWS) 中的相关用户间建立链接关系。

可以通过将 Azure AD 中的“用户名”值分配为 Amazon Web Services (AWS) 中“用户名”的值来建立此链接关系。

若要配置并测试 Amazon Web Services (AWS) 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建一个 Amazon Web 服务的测试用户](#creating-an-amazon-web-services-test-user)** -若要具有在 Amazon Web Services (AWS) 链接到的 Azure AD 表示形式她的 Britta 人 Simon 的副本。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，会在 Azure 门户中启用 Azure AD 单一登录并在 Amazon Web Services (AWS) 应用程序中配置单一登录。

**若要配置 Amazon Web Services (AWS) 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **Amazon Web Services (AWS)** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_samlbase.png)

3. 在“Amazon Web Services (AWS) 域和 URL”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成。

    ![配置单一登录](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_url.png)

4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存 XML 文件。
    
    ![配置单一登录](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_certificate.png)

5. Amazon Web Services (AWS) Software 应用程序需要采用特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。

    ![配置单一登录](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_attribute.png)

6. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    | 属性名称  | 属性值 | 命名空间 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | 角色            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >需要在 Azure AD 中配置用户预配以从 AWS 控制台中提取所有角色。 请参阅下文中的预配步骤。

    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    b.保留“数据库类型”设置，即设置为“共享”。 在“名称”文本框中，键入为该行显示的属性名称。

    ![配置单一登录](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    c. 在“值”列表中，选择为该行显示的属性值。 根据上文中的指定添加 Namespace 值。
    
    d. 单击“确定” 。

7. 单击“保存”按钮在 Azure 中保存设置。

    ![配置单一登录](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. 在其他浏览器窗口中，以管理员身份登录 Amazon Web Services (AWS) 公司站点。

9. 单击“控制台主页”。
   
    ![配置单一登录][11]

10. 从**安全性、标识和合规性**服务中单击“IAM”。
   
    ![配置单一登录][12]

11. 单击“标识提供者”，并单击“创建提供者”。
   
    ![配置单一登录][13]

12. 在“配置提供者”对话框页上，执行以下步骤：
   
    ![配置单一登录][14]
 
    a. 对于“提供者类型”，请选择“SAML”。

    b. 在“提供者名称”文本框中，键入提供者名称（例如：*WAAD*）。

    c. 若要上传下载的元数据文件，请单击“选择文件”。

    d. 单击“下一步”。

13. 在“验证提供者信息”对话框页上，单击“创建”。 
    
    ![配置单一登录][15]

14. 单击“角色”，并单击“创建新角色”。 
    
    ![配置单一登录][16]

15. 在“设置角色名称”对话框中，执行以下步骤： 
    
    ![配置单一登录][17] 

    a. 在“角色名称”文本框中，键入角色名称（例如：*TestUser*）。 

    b. 单击“下一步”。

16. 在“选择角色类型”对话框中，执行以下步骤： 
    
    ![配置单一登录][18] 

    a. 选择“标识提供者角色的访问权限”。 

    b. 在**授予 SAML 提供程序的 Web 单一访问 (WebSSO) 访问权限**部分中，单击“选择”。

17. 在“建立信任”对话框中，执行以下步骤：  
    
    ![配置单一登录][19] 

    a. 对于 SAML 提供者，选择之前创建的 SAML 提供者（例如：*WAAD*）
  
    b. 单击“下一步”。

18. 在“验证角色信任”对话框中，单击“下一步”。
    
    ![配置单一登录][32]

19. 在“附加策略”对话框中，单击“下一步”。
    
    ![配置单一登录][33]

20. 在“审阅”对话框中，执行以下步骤：
    
    ![配置单一登录][34]
 
    a. 单击“创建角色”。

    b. 创建所需数量的角色，并将其映射到标识提供者。

21. 现在，配置用户预配以从 AWS 中提取所有角色

    a. 在 AWS 控制台中，使用根帐户进行登录

    b. 在右上角，单击你的姓名，并单击“我的安全凭据”选项。 这会打开一个屏幕，其中显示了警告消息。 单击“安全凭据”按钮以通过该屏幕。
        
       ![配置单一登录][36]

       ![配置单一登录][37]

    c. 在“访问密钥”部分中，单击“新建访问密钥”按钮。 这会生成访问密钥 ID 和令牌值。
    
       ![配置单一登录][38]

    d. 复制这些值并下载它们，以免丢失它们。

    e. 在 Azure 门户中，在 Amazon Web Services (AWS) 应用程序集成页上，单击“预配”。
        
       ![配置单一登录][35]

    f. 将预配模式设置为“自动”
        
       ![配置单一登录][39]

    g. 现在，在“clientsecret”和“密钥标记”中，粘贴已从 AWS 控制台复制的相应值。
    
    h. 可以单击“测试连接”按钮来测试连接。 在成功后，可以启动预配连接器。
       
       ![配置单一登录][40]

    i. 现在，将预配状态启用为“开启”。 这会开始从应用程序提取角色。

       ![配置单一登录][41]

    > [!NOTE]
    > Azure AD 预配服务每隔一段时间会运行一次来从 AWS 同步角色。 应当会看到所有标识提供者都已将 AWS 角色附加到 Azure AD 中，并且在将应用程序分配给用户或组时可以使用这些角色。

<!--### Next steps

To ensure users can sign-in to Amazon Web Services (AWS) after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Amazon Web Services (AWS) prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Amazon Web Services (AWS) in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Amazon Web Services (AWS) users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-an-amazon-web-services-test-user"></a>创建 Amazon Web Services 测试用户

为了使 Azure AD 用户能够登录到 Amazon Web Services (AWS)，必须将其预配到 Amazon Web Services (AWS) 中。 对于 Amazon Web Services (AWS)，预配任务需要手动完成。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录 **Amazon Web Services (AWS)** 公司站点。

2. 单击“控制台主页”图标。 
   
    ![配置单一登录][11]

3. 单击“标识和访问管理”。 
   
    ![配置单一登录][28]

4. 在仪表板中，单击“用户”，并单击“创建新用户”。 
   
    ![配置单一登录][29]

5. 在“创建用户”对话框页上，执行以下步骤： 
   
    ![配置单一登录][30]   
    
    a. 在“输入用户名称”文本框中，键入 Brita Simon 在 Azure AD 中的用户名 (userprincipalname)。

    b. 单击“创建”。
        
### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过向 Britta Simon 授予对 Amazon Web Services (AWS) 的访问权限使她能够使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Amazon Web Services (AWS)，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Amazon Web Services (AWS)”。

    ![配置单一登录](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“选择角色”选项卡上，为用户选择合适的角色。 所有这些角色都显示有角色名称和标识提供者名称。 因此，可以轻松识别来自 AWS 的角色。

8. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Amazon Web Services (AWS)”磁贴时，应该会自动登录 Amazon Web Services (AWS) 应用程序。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_15.png

[28]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795037.png
[30]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795038.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

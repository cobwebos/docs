---
title: 教程：将 Azure Active Directory 与 Amazon Web Services (AWS) 集成以连接多个帐户 | Microsoft Docs
description: 了解如何在 Azure AD 与 Amazon Web Services (AWS) 的多个帐户之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: jeedes
ms.openlocfilehash: dfbabdc7d5a02b21103ac709b8dbc89d69cde0e0
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054249"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>教程：Azure Active Directory 与多个 Amazon Web Services (AWS) 帐户的集成

本教程介绍如何将 Azure Active Directory (Azure AD) 与 Amazon Web Services (AWS) 的多个帐户集成。

将 Amazon Web Services (AWS) 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Amazon Web Services (AWS)。
- 可以让用户使用其 Azure AD 帐户自动登录到 Amazon Web Services (AWS)（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Amazon Web Services (AWS) 的集成，需要具有以下项：

- 一个 Azure AD Basic 或 Premium 订阅
- 多个已启用单一登录的 Amazon Web Services (AWS) 帐户

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Amazon Web Services (AWS)
2. 配置和测试 Azure AD 单一登录

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>从库中添加 Amazon Web Services (AWS)
要配置 Amazon Web Services (AWS) 与 Azure AD 的集成，需要从库将 Amazon Web Services (AWS) 添加到托管 SaaS 应用列表。

**若要从库中添加 Amazon Web Services (AWS)，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Amazon Web Services (AWS)”，从结果面板中选择“Amazon Web Services (AWS)”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Amazon Web Services (AWS)](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

5. 添加应用程序后，转到“属性”页并复制“对象 ID”。

    ![结果列表中的 Amazon Web Services (AWS)](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Amazon Web Services (AWS) 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Amazon Web Services (AWS) 用户。 换句话说，需在 Azure AD 用户与 Amazon Web Services (AWS) 中的相关用户间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Amazon Web Services (AWS) 中“用户名”的值来建立此链接关系。

若要配置并测试 Amazon Web Services (AWS) 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，会在 Azure 门户中启用 Azure AD 单一登录并在 Amazon Web Services (AWS) 应用程序中配置单一登录。

**若要配置 Amazon Web Services (AWS) 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户的 **Amazon Web Services (AWS)** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. 在“Amazon Web Services (AWS) 域和 URL”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成。

    ![Amazon Web Services (AWS) 域和 URL 单一登录信息](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Amazon Web Services (AWS) Software 应用程序需要采用特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。

    ![配置单一登录属性](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_attribute.png)    

5. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    | 属性名称  | 属性值 | 命名空间 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | 角色            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >需要在 Azure AD 中配置用户预配以从 AWS 控制台中提取所有角色。 请参阅下文中的预配步骤。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录 add](./media/aws-multi-accounts-tutorial/tutorial_attribute_04.png)

    ![配置单一登录属性](./media/aws-multi-accounts-tutorial/tutorial_attribute_05.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 在“值”列表中，选择为该行显示的属性值。

    d. 在“命名空间”文本框中，键入为该行显示的命名空间值。
    
    d. 单击“确定” 。

6. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/aws-multi-accounts-tutorial/tutorial_general_400.png)

8. 在其他浏览器窗口中，以管理员身份登录 Amazon Web Services (AWS) 公司站点。

9. 单击“AWS 主页”。
   
    ![配置单一登录主页][11]

10. 单击“IAM”（标识和访问管理）。 
   
    ![配置单一登录标识][12]

11. 单击“标识提供者”，并单击“创建提供者”。 
   
    ![配置单一登录提供者][13]

12. 在“配置提供者”对话框页上，执行以下步骤： 
   
    ![配置单一登录对话框][14]
 
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 对于“提供者类型”，请选择“SAML”。

    b. 在“提供者名称”文本框中，键入提供者名称（例如：*WAAD*）。

    c. 若要上传从 Azure 门户下载的**元数据文件**，请单击“选择文件”。

    d. 单击“下一步”。

13. 在“验证提供者信息”对话框页上，单击“创建”。 
    
    ![配置单一登录验证][15]

14. 单击“角色”，然后单击“创建角色”。 
    
    ![配置单一登录角色][16]

15. 在“创建角色”页中，执行以下步骤：  
    
    ![配置单一登录信任][19] 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“选择可信实体的类型”下选择“SAML 2.0 联合身份验证”。

    b. 在“选择 SAML 2.0 提供程序”部分中，选择之前创建的 SAML 提供程序（例如：*WAAD*）

    c. 选择“允许以编程方式和通过 AWS 管理控制台进行访问”。
  
    d. 单击“下一步: 权限”。

16. 在“附加权限策略”对话框中，单击“下一步: 复查”。  
    
    ![配置单一登录策略][33]

17. 在“审阅”对话框中，执行以下步骤：   
    
    ![配置单一登录审阅][34] 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“角色名称”文本框中，输入你的角色名称。

    b. 在“角色说明”文本框中，输入说明。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“创建角色”。

    b. 创建所需数量的角色，并将其映射到标识提供者。

18. 从当前 AWS 帐户注销，使用要在其中配置 Azure AD 单一登录的另一个帐户登录。

19. 执行步骤 9 到步骤 17，创建要为此帐户设置的多个角色。 如果有两个以上的帐户，请对所有帐户执行相同的步骤，以便为这些帐户创建角色。

20. 在帐户中创建所有角色后，这些角色将显示在这些帐户的“角色”列表中。

    ![角色设置](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

21. 我们需要捕获所有帐户中所有角色的所有“角色 ARN”和“受信任实体”，并需要将其手动映射到 Azure AD 应用程序。 

22. 单击角色复制“角色 ARN”和“受信任实体”值。 对于要在 Azure AD 中创建的所有角色，需要使用这些值。

    ![角色设置](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)
 
23. 针对所有帐户中的所有角色执行上述步骤，并以 **<角色 ARN>,<受信任实体>** 的格式将其存储在记事本中。 

24. 在另一个窗口中打开 [Azure AD Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 使用租户的全局管理员/共同管理员凭据登录到 Graph 浏览器站点。

    b. 需要拥有足够的权限才能创建角色。 单击“修改权限”以获取所需的权限。 

    ![Graph 浏览器对话框](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. 从列表中选择以下权限（如果尚未这样做），然后单击“修改权限” 

    ![Graph 浏览器对话框](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. 系统会要求重新登录并接受许可。 接受许可后，再次登录到 Graph 浏览器。

    e. 在下拉列表中将版本更改为 **beta**。 若要从租户提取所有服务主体，请使用以下查询：
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    如果使用多个目录，则可以使用包含主域的以下模式：`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph 浏览器对话框](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)
    
    f. 从提取的服务主体的列表中，获取需要修改的那一个。 还可使用 Ctrl+F 从列出的所有服务主体中搜索应用程序。 可以使用从“Azure AD 属性”页复制的“对象 ID”运行以下查询，转到相应的服务主体。
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`。

    ![Graph 浏览器对话框](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. 从服务主体对象中提取 appRoles 属性。 

    ![Graph 浏览器对话框](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. 现在需要为应用程序生成新角色。 

    i. 下面 JSON 是 appRoles 对象的示例。 创建类似的对象，以添加应用程序所需的角色。 

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > 只能在完成修补操作的 **msiam_access** 之后添加新角色。 此外，可以根据组织的需要添加任意数量的角色。 Azure AD 将在 SAML 响应中将这些角色的**值**作为声明值进行发送。
    
    j. 返回到 Graph 浏览器，将方法从 **GET** 更改为 **PATCH**。 通过更新 appRoles 属性（类似于上面示例中所示的属性）来修补服务主体对象以获取所需的角色。 单击“运行查询”执行此修补操作。 随后会显示一条成功消息，确认已创建 Amazon Web Services 应用程序的角色。

    ![Graph 浏览器对话框](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

25. 使用更多角色修补服务主体后，可将用户/组分配到相应的角色。 可通过转到门户并导航到 Amazon Web Services 应用程序来完成此操作。 在顶部单击“用户和组”选项卡。 

26. 我们建议为每个 AWS 角色创建新组，以便可以在该组中分配该特定角色。 请注意，这是一个组到一个角色的一对一映射。 然后，可以添加属于该组的成员。

27. 创建组后，请选择该组并分配到应用程序。 

    ![配置单一登录 Add](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

28. 若要将角色分配到组，请选择该角色，然后单击页面底部的“分配”按钮。

    ![配置单一登录 Add](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

> [!Note]
> 请注意，需要在 Azure 门户中刷新会话才能看到新角色。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Amazon Web Services (AWS)”磁贴时，应会看到 Amazon Web Services (AWS) 应用程序页，其中包含用于选择角色的选项。

![配置单一登录 Add](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

此外，还可以验证 SAML 响应，以查看角色是否作为声明传递。

![配置单一登录 Add](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/aws-multi-accounts-tutorial/tutorial_general_01.png
[2]: ./media/aws-multi-accounts-tutorial/tutorial_general_02.png
[3]: ./media/aws-multi-accounts-tutorial/tutorial_general_03.png
[4]: ./media/aws-multi-accounts-tutorial/tutorial_general_04.png

[100]: ./media/aws-multi-accounts-tutorial/tutorial_general_100.png

[200]: ./media/aws-multi-accounts-tutorial/tutorial_general_200.png
[201]: ./media/aws-multi-accounts-tutorial/tutorial_general_201.png
[202]: ./media/aws-multi-accounts-tutorial/tutorial_general_202.png
[203]: ./media/aws-multi-accounts-tutorial/tutorial_general_203.png
[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_on.png


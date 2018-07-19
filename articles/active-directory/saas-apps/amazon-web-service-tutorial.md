---
title: 教程：Azure Active Directory 与 Amazon Web Services (AWS) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Amazon Web Services (AWS) 之间配置单一登录。
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
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 60d2f8109fbd5f11042d915dc7f43f3c9dd602d5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048890"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>教程：Azure Active Directory 与 Amazon Web Services (AWS) 集成

本教程介绍了如何将 Amazon Web Services (AWS) 与 Azure Active Directory (Azure AD) 进行集成。

将 Amazon Web Services (AWS) 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Amazon Web Services (AWS)。
- 可以让用户使用其 Azure AD 帐户自动登录到 Amazon Web Services (AWS)（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Amazon Web Services (AWS) 的集成，需要具有以下项：

- Azure AD 订阅
- 已启用 Amazon Web Services (AWS) 单一登录的订阅

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

    ![结果列表中的 Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Amazon Web Services (AWS) 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Amazon Web Services (AWS) 用户。 换句话说，需在 Azure AD 用户与 Amazon Web Services (AWS) 中的相关用户间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Amazon Web Services (AWS) 中“用户名”的值来建立此链接关系。

若要配置并测试 Amazon Web Services (AWS) 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Amazon Web Services (AWS) 测试用户](#create-an-amazon-web-services-aws-test-user)**：在 Amazon Web Services (AWS) 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，会在 Azure 门户中启用 Azure AD 单一登录并在 Amazon Web Services (AWS) 应用程序中配置单一登录。

**若要配置 Amazon Web Services (AWS) 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户的 **Amazon Web Services (AWS)** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. 在“Amazon Web Services (AWS) 域和 URL”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成。

    ![Amazon Web Services (AWS) 域和 URL 单一登录信息](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Amazon Web Services (AWS) Software 应用程序需要采用特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。

    ![配置单一登录 attb](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png) 

5. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    | 属性名称  | 属性值 | 命名空间 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | 角色            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >需要在 Azure AD 中配置用户预配以从 AWS 控制台中提取所有角色。 请参阅下文中的预配步骤。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录 add](./media/amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![配置单一登录 addattb](./media/amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 在“值”列表中，选择为该行显示的属性值。

    d. 在“命名空间”文本框中，键入为该行显示的命名空间值。
    
    d. 单击“确定” 。

6. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/amazon-web-service-tutorial/tutorial_general_400.png)

8. 在其他浏览器窗口中，以管理员身份登录 Amazon Web Services (AWS) 公司站点。

9. 单击“AWS 主页”。
   
    ![配置单一登录主页][11]

10. 单击“标识和访问管理”。 
   
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

16. 在“附加权限策略”对话框中，不需要附加任何策略。 单击“下一步: 核对”。  
    
    ![配置单一登录策略][33]

17. 在“审阅”对话框中，执行以下步骤：   
    
    ![配置单一登录审阅][34] 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“角色名称”文本框中，输入你的角色名称。

    b. 在“角色说明”文本框中，输入说明。

    c. 单击“创建角色”。

    d. 创建所需数量的角色，并将其映射到标识提供者。

18. 在 Azure AD 用户预配中使用 AWS 服务帐户凭据从 AWS 帐户提取角色。 为此，请打开 AWS 控制台主页。

19. 单击“服务” -> “安全、标识和符合性” -> “IAM”。

    ![从 AWS 帐户提取角色](./media/amazon-web-service-tutorial/fetchingrole1.png)

20. 在 IAM 部分选择“策略”选项卡。

    ![从 AWS 帐户提取角色](./media/amazon-web-service-tutorial/fetchingrole2.png)

21. 通过单击“创建策略”来创建用于在 Azure AD 用户预配中从 AWS 帐户提取角色的新策略。

    ![创建新策略](./media/amazon-web-service-tutorial/fetchingrole3.png)

22. 创建自己的策略，通过执行下列步骤从 AWS 帐户提取所有角色：

    ![创建新策略](./media/amazon-web-service-tutorial/policy1.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“创建策略”部分中单击“JSON”选项卡。

    b. 在策略文档中，添加以下 JSON。
    
    ```
    
    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",
        
    "Action": [
        
    "iam:ListRoles"
        
    ],

    "Resource": "*"

    }

    ]

    }
    
    ```

    c. 单击“查看策略”按钮以验证策略。

    ![定义新策略](./media/amazon-web-service-tutorial/policy5.png)

23. 通过执行以下步骤来定义新策略：

    ![定义新策略](./media/amazon-web-service-tutorial/policy2.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 提供 **AzureAD_SSOUserRole_Policy** 作为“策略名称”。

    b. 可以提供“此策略将允许从 AWS 帐户提取角色”作为策略的“说明”。
    
    c. 单击“创建策略”按钮。

24. 通过执行以下步骤，在 AWS IAM 服务中创建新的用户帐户：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在 AWS IAM 控制台中单击“用户”导航。

    ![定义新策略](./media/amazon-web-service-tutorial/policy3.png)
    
    b. 单击“添加用户”按钮创建新用户。

    ![添加用户](./media/amazon-web-service-tutorial/policy4.png)

    c. 在“添加用户”部分中，执行以下步骤：
    
    ![添加用户](./media/amazon-web-service-tutorial/adduser1.png)
    
    * 输入 **AzureADRoleManager** 作为用户名。
    
    * 在“访问类型”中，选择“编程访问”选项。 这样，用户就可以调用 API 并从 AWS 帐户中提取角色。
    
    * 单击右下角的“后续权限”按钮。

25. 现在，通过执行以下步骤为此用户创建新策略：

    ![添加用户](./media/amazon-web-service-tutorial/adduser2.png)
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“直接附加现有策略”按钮。

    b. 在筛选器部分中搜索新创建的策略 **AzureAD_SSOUserRole_Policy**。
    
    c. 选择“策略”，然后单击“下一步: 审阅”按钮。

26. 通过执行以下步骤，审阅附加用户的策略：

    ![添加用户](./media/amazon-web-service-tutorial/adduser3.png)
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 审阅映射到该用户的用户名、访问类型和策略。
    
    b. 单击右下角的“创建用户”按钮创建用户。

27. 通过执行以下步骤，下载用户的用户凭据：

    ![添加用户](./media/amazon-web-service-tutorial/adduser4.png)
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 复制用户的“访问密钥 ID”和“密码访问密钥”。
    
    b. 在 Azure AD 用户预配部分输入这些凭据，以便从 AWS 控制台提取角色。
    
    c. 单击底部的“关闭”按钮。

28. 在 Azure AD 管理门户中导航到 Amazon Web Services 应用的“用户预配”部分。

    ![添加用户](./media/amazon-web-service-tutorial/provisioning.png)

29. 分别在“客户端密码”和“密码令牌”字段中输入**访问密钥**和**密码**。

    ![添加用户](./media/amazon-web-service-tutorial/provisioning1.png)
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“客户端密码”字段中输入 AWS 用户访问密钥。
    
    b. 在“密码令牌”字段中输入 AWS 用户密码。
    
    c. 单击“测试连接”按钮，应当能够成功测试此连接。

    d. 单击顶部的“保存”按钮保存此设置。
 
30. 现在，确保在“设置”部分中将“预配状态”设置为“开”，方法是打开开关，然后单击顶部的“保存”按钮。

    ![添加用户](./media/amazon-web-service-tutorial/provisioning2.png)

> [!Note]
> 如果要将多个 AWS 帐户集成到一个 Azure 帐户以进行单一登录，请参阅[此](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial)文。 


### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/amazon-web-service-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/amazon-web-service-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/amazon-web-service-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/amazon-web-service-tutorial/create_aaduser_04.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>创建 Amazon Web Services (AWS) 测试用户

本部分要在 Amazon Web Services (AWS) 中创建名为“Britta Simon”的测试用户。 Amazon Web Services (AWS) 不需要在其系统中创建用户进行 SSO，因此，你无需在此处执行任何操作。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予对 Amazon Web Services (AWS) 的访问权限，使她能够使用 Azure 单一登录。

![分配用户角色][200] 

**要将 Britta Simon 分配到 Amazon Web Services (AWS)，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Amazon Web Services (AWS)”。

    ![应用程序列表中的 Amazon Web Services (AWS) 链接](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Amazon Web Services (AWS)”磁贴时，应该会自动登录 Amazon Web Services (AWS) 应用程序。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

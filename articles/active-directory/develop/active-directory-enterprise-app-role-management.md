---
title: 在 Azure Active Directory 中为企业应用程序配置 SAML 令牌中颁发的角色声明 | Microsoft Docs
description: 了解如何在 Azure Active Directory 中为企业应用程序配置 SAML 令牌中颁发的角色声明
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 5520781c9f687e5e32ad1d8c38922a456bb5e3ce
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="configuring-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>在 Azure Active Directory 中为企业应用程序配置 SAML 令牌中颁发的角色声明

通过此功能，用户可为响应令牌中的“角色”声明自定义声明类型，该令牌是使用 Azure AD 对应用进行授权时收到的令牌。

## <a name="prerequisites"></a>先决条件
- 具有目录安装的 Azure AD 订阅
- 启用了单一登录的订阅
- 必须使用应用程序配置 SSO

## <a name="when-to-use-this-feature"></a>何时使用此功能

如果应用程序预期 SAML 响应中会传递自定义角色，则需要使用此功能。 通过此功能，可创建尽可能多的角色，以便从 Azure AD 传递回应用程序。

## <a name="steps-to-use-this-feature"></a>使用此功能的步骤

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入应用程序的名称，从结果面板中选择应用程序，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的应用程序](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. 添加应用程序后，立即转到“属性”页并复制“对象 ID”

    ![“属性”页](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. 在另一个窗口中打开 [Azure AD Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)。

    a. 使用租户的全局管理员/共同管理员凭据登录到 Graph 浏览器站点。

    b. 将版本更改为“beta”，然后使用以下查询从租户中提取服务主体列表：
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    如果正在使用多个目录，则应遵循此模式 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    c. 从提取的服务主体列表中，获取需要修改的服务主体。 还可使用 Ctrl+F 从列出的所有服务主体中搜索应用程序。 搜索已从“属性”页复制的“对象 ID”，并使用以下查询转到相应的服务主体。
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`。

    d. 从服务主体对象中提取 appRoles 属性。

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    e. 现在需要为应用程序生成新角色。 可从[此处](https://app.box.com/s/jw6m9p9ehmf4ut5jx8xhcw87cu09ml3y)下载 Azure AD 角色生成器。

    f. 打开 Azure AD 生成器并执行以下步骤 -

    ![Azure AD 生成器](./media/active-directory-enterprise-app-role-management/azure_ad_role_generator.png)
    
    输入“角色名称”、“角色说明”和“角色值”。 单击“添加”以添加该角色
    
    添加完所需角色后，单击“生成”
    
    单击“复制内容”以复制该内容

    > [!NOTE] 
    > 请确保拥有“msiam_access”用户角色且该 ID 在生成的角色中相匹配。

    g. 返回到 Graph 浏览器。 将方法从“GET”更改为“PATCH”。 通过使用复制的值更新 appRoles 属性来修补服务主体对象以获得所需的 appRoles。 单击“运行查询”。

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-patch.png)

    > [!NOTE]
    > 以下是一个 appRoles 对象的示例。 
    > 
    >   ```
    > {
    > "appRoles": [
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "msiam_access",
    >   "displayName": "msiam_access",
    >   "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
    >   "isEnabled": true,
    >   "origin": "Application",
    >   "value": null
    > },
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "teacher",
    >   "displayName": "teacher",
    >   "id": "6478ffd2-5dbd-4584-b2ce-137390b09b60",
    >   "isEnabled": ,
    >   "origin": "ServicePrincipal",
    >   "value": "teacher"
    > }
    > ] 
    > }
    >
    >   ```

7. 使用更多角色修补服务主体后，我们可将用户分配到相应的角色。 可通过转到门户并导航到相应的应用来完成此操作。 然后，单击顶部的“用户和组”选项卡。 此进程将列出所有用户或组。

    ![配置单一登录 Add](./media/active-directory-enterprise-app-role-management/userrole.png)

    a. 若要将角色分配给某个用户，只需选择特定的用户/组，然后单击页面底部的“分配”按钮。

    ![配置单一登录 Add](./media/active-directory-enterprise-app-role-management/userandgroups.png)

    b. 单击该按钮会弹出一个窗口，可从其中为各个服务主体定义的不同角色中选择一个角色。

    c. 选择需要的角色并单击“提交”。

8. 将角色分配给用户后，我们需要更新“属性”表以定义“角色”声明的自定义映射。

9. 在“单一登录”对话框的“用户属性”部分，按图中所示配置 SAML 令牌属性，然后执行以下步骤：
    
    | 属性名称 | 属性值 |
    | -------------- | ----------------|    
    | 角色名称      | user.assignedrole |

    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录 Add](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![配置单一登录属性](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 在“值”列表中，选择为该行显示的属性值。

    d. 将“命名空间”留空。
    
    e. 单击“确定” 。

10. 若要在 IDP 启动的“单一登录”中测试应用程序，请登录“访问面板”(https://myapps.microsoft.com) 并单击应用程序磁贴。 在 SAML 令牌中，应该会看到具有所提供的声明名称的用户的所有已分配角色。

## <a name="update-existing-role"></a>更新现有角色

1. 若要更新现有角色，请执行以下步骤 -

    a. 在另一个窗口中打开 [Azure AD Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)。

    b. 使用租户的全局管理员/共同管理员凭据登录到 Graph 浏览器站点。
    
    c. 将版本更改为“beta”，然后使用以下查询从租户中提取服务主体列表：
    
    `https://graph.microsoft.com/beta/servicePrincipals`
        
    如果正在使用多个目录，则应遵循此模式 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. 从提取的服务主体列表中，获取需要修改的服务主体。 还可使用 Ctrl+F 从列出的所有服务主体中搜索应用程序。 搜索已从“属性”页复制的“对象 ID”，并使用以下查询转到相应的服务主体。
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`。
    
    e. 从服务主体对象中提取 appRoles 属性。
    
    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)
    
    f. 若要更新现有角色，请遵循以下步骤：

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * 将方法从“GET”更改为“PATCH”。

    * 从应用程序中复制现有角色，并将其粘贴到“请求正文”中。
    
    * 根据组织要求替换“角色说明”、“角色值”和“角色显示名称”，从而更新角色值。
    
    * 更新所有要求的角色后，请单击“运行查询”。
        
## <a name="delete-existing-role"></a>删除现有角色

1. 若要删除现有角色，请执行以下步骤 -

    a. 在另一个窗口中打开 [Azure AD Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)。

    b. 使用租户的全局管理员/共同管理员凭据登录到 Graph 浏览器站点。

    c. 将版本更改为“beta”，然后使用以下查询从租户中提取服务主体列表：
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    如果正在使用多个目录，则应遵循此模式 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. 从提取的服务主体列表中，获取需要修改的服务主体。 还可使用 Ctrl+F 从列出的所有服务主体中搜索应用程序。 搜索已从“属性”页复制的“对象 ID”，并使用以下查询转到相应的服务主体。
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`。
    
    e. 从服务主体对象中提取 appRoles 属性。
    
    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    f. 若要删除现有角色，请遵循以下步骤：

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-patchdelete.png)

    将方法从“GET”更改为“PATCH”。

    从应用程序中复制现有角色，并将其粘贴到“请求正文”中。
    
    将想要删除的角色的“IsEnabled”值设置为“false”

    单击“运行查询”。
    
    > [!NOTE] 
    > 请确保拥有“msiam_access”用户角色且该 ID 在生成的角色中相匹配。
    
    g. 执行上述进程后，请将方法保持为“PATCH”，并在“请求正文”中粘贴剩余的角色内容，然后单击“运行查询”。
    
    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-patchfinal.png)

    h. 运行查询之后，将删除该角色。
    
    > [!NOTE]
    > 需要先禁用该角色，然后才能将其删除。 

## <a name="next-steps"></a>后续步骤

有关其他步骤，请参阅[应用文档](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)。

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png

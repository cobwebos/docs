---
title: 在 Azure Active Directory 中为企业应用程序配置在 SAML 令牌中颁发的角色声明 | Microsoft Docs
description: 了解如何在 Azure Active Directory 中为企业应用程序配置在 SAML 令牌中颁发的角色声明
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
ms.openlocfilehash: 58656e2aa3b052d9bd9aa14edeb6215858d55ea4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="configuring-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>在 Azure Active Directory 中为企业应用程序配置在 SAML 令牌中颁发的角色声明

此功能允许用户使用 Azure AD 自定义在对应用进行授权时收到的响应令牌中的“角色”声明的声明类型。

## <a name="prerequisites"></a>先决条件
- 一个安装了该目录的 Azure AD 订阅
- 一个启用了单一登录的订阅
- 必须为你的应用程序配置 SSO

## <a name="when-to-use-this-feature"></a>何时使用此功能

如果应用程序预期会在 SAML 响应中传递自定义角色，则需要使用此功能。 此功能允许你创建要从 Azure AD 传递回应用程序的所需数量的角色。

## <a name="steps-to-use-this-feature"></a>使用此功能的步骤

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入你的应用程序的名称，从结果面板中选择你的应用程序，然后单击“添加”按钮来添加该应用程序。

    ![结果列表中的应用程序](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. 在添加应用程序后，转到“属性”页并复制**对象 ID**。

    ![“属性”页](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. 在另一个窗口中打开 [Azure AD Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)。

    a. 使用你的租户的全局管理员/共同管理员凭据登录到 Graph 浏览器。

    b. 将版本更改为 **beta** 并使用以下查询从你的租户中提取服务主体列表：
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    如果你在使用多个目录，则应遵循以下模式：`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    c. 从提取的服务主体的列表中，获取需要修改的那个。 还可以使用 Ctrl + F 来从所有列出的服务主体中搜索该应用程序。 搜索已从“属性”页复制的**对象 ID** 并使用以下查询来到达相应的服务主体。
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`。

    d.单击“下一步”。 从服务主体对象中提取 appRoles 属性。

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    e. 现在，你需要为应用程序生成新角色。 可以从[此处](https://app.box.com/s/jw6m9p9ehmf4ut5jx8xhcw87cu09ml3y)下载 Azure AD 角色生成器。

    f. 打开 Azure AD 生成器并执行以下步骤 -

    ![Azure AD 生成器](./media/active-directory-enterprise-app-role-management/azure_ad_role_generator.png)
    
    输入**角色名称**、**角色说明**和**角色值**。 单击“添加”以添加该角色
    
    添加所需的所有角色后，单击“生成”
    
    通过单击“复制内容”来复制内容

    > [!NOTE] 
    > 请确保你具有 **msiam_access** 用户角色并且 ID 在生成的角色中匹配。

    g. 返回到 Graph 浏览器。 将方法从 **GET** 更改为 **PATCH**。 通过使用复制的值更新 appRoles 属性来修补服务主体对象，使其具有所需的 appRoles 单击“运行查询”。

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-patch.png)

    > [!NOTE]
    > 下面是 appRoles 对象的一个示例。 
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
        "description": "teacher",
        "displayName": "teacher",
        "id": "6478ffd2-5dbd-4584-b2ce-137390b09b60",
        "isEnabled": ,
        "origin": "ServicePrincipal",
        "value": "teacher"
    }
    ] 
    }   
    ```
7. 在使用更多角色修补服务主体后，可以将用户分配到相应的角色。 可以通过转到门户并导航到相应的应用来完成此操作。 然后，单击顶部的“用户和组”选项卡。 此过程将列出所有用户或组。

    ![配置单一登录 Add](./media/active-directory-enterprise-app-role-management/userrole.png)

    a. 若要将某个角色分配给任何用户，只需选择特定的用户/组，然后单击页面底部的“分配”按钮。

    ![配置单一登录 Add](./media/active-directory-enterprise-app-role-management/userandgroups.png)

    b. 单击该按钮将显示一个弹出窗口，可以在其中从为相应服务主体定义的不同角色中选择角色。

    c. 选择所需的角色并单击“提交”。

8. 将角色分配给用户后，需要更新“属性”表来定义**角色**声明的自定义映射。

9. 在“单一登录”对话框的“用户属性”部分，按图中所示配置 SAML 令牌属性，然后执行以下步骤：
    
    | 属性名称 | 属性值 |
    | -------------- | ----------------|    
    | 角色名称      | user.assignedrole |

    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录 Add](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![配置单一登录属性](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 在“值”列表中，选择为该行显示的属性值。

    d.单击“下一步”。 将“命名空间”留空。
    
    e. 单击“确定” 。

10. 若要在 IDP 启动的单一登录中测试你的应用程序，请登录到访问面板 (https://myapps.microsoft.com) 并单击你的应用程序磁贴。 在 SAML 令牌中，应该会看到具有你提供的声明名称的用户的所有已分配角色。

## <a name="update-existing-role"></a>更新现有角色

1. 若要更新现有角色，请执行以下步骤 -

    a. 在另一个窗口中打开 [Azure AD Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)。

    b. 使用你的租户的全局管理员/共同管理员凭据登录到 Graph 浏览器。
    
    c. 将版本更改为 **beta** 并使用以下查询从你的租户中提取服务主体列表：
    
    `https://graph.microsoft.com/beta/servicePrincipals`
        
    如果你在使用多个目录，则应遵循以下模式：`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d.单击“下一步”。 从提取的服务主体的列表中，获取需要修改的那个。 还可以使用 Ctrl + F 来从所有列出的服务主体中搜索该应用程序。 搜索已从“属性”页复制的**对象 ID** 并使用以下查询来到达相应的服务主体。
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`。
    
    e. 从服务主体对象中提取 appRoles 属性。
    
    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)
    
    f. 若要更新现有的角色，请遵循以下步骤：

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * 将方法从 **GET** 更改为 **PATCH**。

    * 从应用程序中复制现有角色并将其粘贴到“请求正文”中。
    
    * 根据你的组织要求，替换**角色说明**、**角色值**和**角色显示名称**，从而更新角色的值。
    
    * 更新所需的所有角色后，单击“运行查询”。
        
## <a name="delete-existing-role"></a>删除现有角色

若要删除现有角色，请执行以下步骤：

a. 在另一个窗口中打开 [Azure AD Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)。

b. 使用你的租户的全局管理员/共同管理员凭据登录到 Graph 浏览器。

c. 将版本更改为 **beta** 并使用以下查询从你的租户中提取服务主体列表：
    
`https://graph.microsoft.com/beta/servicePrincipals`
    
如果你在使用多个目录，则应遵循以下模式：`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
d.单击“下一步”。 从提取的服务主体的列表中，获取需要修改的那个。 还可以使用 Ctrl + F 来从所有列出的服务主体中搜索该应用程序。 搜索已从“属性”页复制的**对象 ID** 并使用以下查询来到达相应的服务主体。
     
`https://graph.microsoft.com/beta/servicePrincipals/<objectID>`。
    
e. 从服务主体对象中提取 appRoles 属性。
    
![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

f. 若要删除现有角色，请遵循以下步骤：

![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-patchdelete.png)

将方法从 **GET** 更改为 **PATCH**。

从应用程序中复制现有角色，并将其粘贴到“请求正文”中。
    
针对要删除的角色将 **IsEnabled** 值设置为 **false**

单击“运行查询”。
    
> [!NOTE] 
> 请确保你具有 **msiam_access** 用户角色并且 ID 在生成的角色中匹配。
    
g. 执行上述过程后，将方法保持为 **PATCH** 并将剩余角色内容粘贴在“请求正文”中，然后单击“运行查询”。
    
![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-patchfinal.png)

h. 在运行查询后，该角色将被删除。
    
> [!NOTE]
> 需要先禁用该角色，然后才能将其删除。 

## <a name="next-steps"></a>后续步骤

参阅[应用文档](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-tutorial-list)来了解其他步骤。

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png

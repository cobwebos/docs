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
ms.openlocfilehash: 3acfa51351ac49456f5f9fcac8aa4f4f339b9ea3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
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

5. 添加应用程序后，转到“属性”页并复制“对象 ID”。

    ![“属性”页](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. 在另一个窗口中打开 [Azure AD Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)。

    a. 使用租户的全局管理员/共同管理员凭据登录到 Graph 浏览器站点。

    b. 需要拥有足够的权限才能创建角色。 单击“修改权限”以获取所需的权限。 

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. 从列表中选择以下权限（如果尚未这样做），然后单击“修改权限” 

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. 系统会要求重新登录并接受许可。 接受许可后，你会再次登录到系统。

    e. 将版本更改为“beta”，然后使用以下查询从租户中提取服务主体列表：
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    如果正在使用多个目录，则应遵循此模式 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. 从提取的服务主体列表中，获取需要修改的服务主体。 还可使用 Ctrl+F 从列出的所有服务主体中搜索应用程序。 搜索已从“属性”页复制的“对象 ID”，并使用以下查询转到相应的服务主体。
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`。

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. 从服务主体对象中提取 appRoles 属性。 

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

    > [!Note]
    > 如果使用的是自定义（非库）应用，则会看到两个默认角色 - User 和 msiam_access。 对于库应用，msiam_access 是唯一的默认角色。 不需要对默认角色进行任何更改。

    h. 现在需要为应用程序生成新角色。 

    i. 下面 JSON 是 appRoles 对象的示例。 请创建一个类似的对象来为应用程序添加所需的角色。 

    ```
    {
       "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Administrators Only",
            "displayName": "Admin",
            "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "Administrator"
        }
    ],
    }
    ```
    > [!Note]
    > 对于修补操作，只能在 **msiam_access** 之后添加新角色。 此外，可以根据组织的需要添加任意数量的角色。 Azure AD 将在 SAML 响应中将这些角色的**值**作为声明值进行发送。
    
    j. 返回到 Graph 浏览器，将方法从 **GET** 更改为 **PATCH**。 通过更新 appRoles 属性（类似于上面示例中所示的属性）来修补服务主体对象以获取所需的角色。 单击“运行查询”执行此修补操作。 一条成功消息会确认角色已创建。

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. 在使用更多角色修补服务主体后，可以将用户分配到相应的角色。 可通过转到门户并导航到相应的应用来完成此操作。 单击顶部的“用户和组”选项卡。 这将列出已分配到应用程序的所有用户和组。 可以在新角色上添加新用户，还可以选择现有用户并单击“编辑”来更改角色。

    ![配置单一登录 Add](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

     若要将角色分配给任意用户，请选择新角色，然后单击页面底部的“分配”按钮。

    ![配置单一登录 Add](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > 请注意，需要在 Azure 门户中刷新会话才能看到新角色。

8. 将角色分配给用户后，我们需要更新“属性”表以定义“角色”声明的自定义映射。

9. 在“单一登录”对话框的“用户属性”部分，按图中所示配置 SAML 令牌属性，然后执行以下步骤：
    
    | 属性名称 | 属性值 |
    | -------------- | ----------------|    
    | 角色名称      | user.assignedrole |

    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录 Add](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![配置单一登录属性](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. 在“名称”文本框中，根据需要键入属性名称。 在此示例中，我们使用**角色名称**作为声明名称。

    c. 在“值”列表中，选择为该行显示的属性值。

    d. 将“命名空间”留空。
    
    e. 单击“确定” 。

10. 若要在 IDP 启动的“单一登录”中测试应用程序，请登录“访问面板”(https://myapps.microsoft.com) 并单击应用程序磁贴。 在 SAML 令牌中，应该会看到具有你提供的声明名称的用户的所有已分配角色。

## <a name="update-existing-role"></a>更新现有角色

若要更新现有角色，请执行以下步骤 -

1. 打开 [Azure AD Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)。

2. 使用租户的全局管理员/共同管理员凭据登录到 Graph 浏览器站点。
    
3. 将版本更改为“beta”，然后使用以下查询从租户中提取服务主体列表：
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    如果正在使用多个目录，则应遵循此模式 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. 从提取的服务主体的列表中，获取需要修改的那一个。 还可使用 Ctrl+F 从列出的所有服务主体中搜索应用程序。 搜索已从“属性”页复制的“对象 ID”，并使用以下查询转到相应的服务主体。
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`。

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. 从服务主体对象中提取 appRoles 属性。
    
    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. 若要更新现有角色，请遵循以下步骤：

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * 将方法从“GET”更改为“PATCH”。

    * 复制现有角色，并将其粘贴到“请求正文”中。

    * 通过根据需要更新**角色说明**、**角色值**或**角色显示名称**来更新角色的值。

    * 更新所有要求的角色后，请单击“运行查询”。
        
## <a name="delete-existing-role"></a>删除现有角色

若要删除现有角色，请执行以下步骤：

1. 在另一个窗口中打开 [Azure AD Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)。

2. 使用租户的全局管理员/共同管理员凭据登录到 Graph 浏览器站点。

3. 将版本更改为“beta”，然后使用以下查询从租户中提取服务主体列表：
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    如果正在使用多个目录，则应遵循此模式 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. 从提取的服务主体列表中，获取需要修改的服务主体。 还可使用 Ctrl+F 从列出的所有服务主体中搜索应用程序。 搜索已从“属性”页复制的“对象 ID”，并使用以下查询转到相应的服务主体。
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`。

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. 从服务主体对象中提取 appRoles 属性。
    
    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. 若要删除现有角色，请遵循以下步骤：

    ![Graph 浏览器对话框](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    * 将方法从“GET”更改为“PATCH”。

    * 从应用程序中复制现有角色，并将其粘贴到“请求正文”中。
        
    * 针对要删除的角色将 **IsEnabled** 值设置为 **false**

    * 单击“运行查询”。
    
    > [!NOTE] 
    > 请确保拥有“msiam_access”用户角色且该 ID 在生成的角色中相匹配。
    
7. 在创建角色后，从 appRoles 部分中删除该角色块，将方法保留为 **PATCH**，然后单击“运行查询”。
    
8. 运行查询之后，将删除该角色。
    
    > [!NOTE]
    > 需要先禁用该角色，然后才能将其删除。 

## <a name="next-steps"></a>后续步骤

有关其他步骤，请参阅[应用文档](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-tutorial-list)。

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png

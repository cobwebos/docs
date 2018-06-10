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
ms.date: 05/30/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 5aa716f91a3155e81ef8dc7c436b4a9a5811238b
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34723246"
---
# <a name="configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>在 Azure Active Directory 中为企业应用程序配置 SAML 令牌中颁发的角色声明

可以使用 Azure Active Directory (Azure AD) 为完成应用授权后收到的响应令牌中的角色声明自定义声明类型。

## <a name="prerequisites"></a>先决条件
- 安装了目录的 Azure AD 订阅。
- 启用了单一登录 (SSO) 的订阅。 必须使用应用程序配置 SSO。

## <a name="when-to-use-this-feature"></a>何时使用此功能

如果应用程序预期 SAML 响应中会传递自定义角色，则需要使用此功能。 可创建尽可能多的角色，以便将其从 Azure AD 传递回应用程序。

## <a name="create-roles-for-an-application"></a>为应用程序创建角色

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”图标。 

    ![Azure Active Directory 图标][1]

2. 选择“企业应用程序”。 然后选择“所有应用程序”。

    ![“企业应用程序”窗格][2]
    
3. 若要添加新应用程序，请选择对话框顶部的“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中键入应用程序的名称，然后从结果面板中选择应用程序。 选择“添加”按钮，添加该应用程序。

    ![结果列表中的应用程序](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. 添加应用程序后，转到“属性”页并复制对象 ID。

    ![“属性”页](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. 在另一个窗口中打开 [Azure AD Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)，然后执行以下步骤：

    a. 使用租户的全局管理员或共同管理员凭据登录到 Graph 浏览器站点。

    b. 需要足够的权限才能创建角色。 选择“修改权限”以获取权限。 

      ![“修改权限”按钮](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. 从列表中选择以下权限（如果还没有这些权限），然后选择“修改权限”。 

      ![权限列表和“修改权限”按钮](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. 接受许可。 此时会再次登录到系统。

    e. 将版本更改为“beta”，然后使用以下查询从租户中提取服务主体列表：
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
      如果使用的是多个目录，则请遵循此模式：`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
      ![Graph 浏览器对话框，包含用于提取服务主体的查询](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. 从提取的服务主体的列表中，获取需要修改的那一个。 还可使用 Ctrl+F 从列出的所有服务主体中搜索应用程序。 搜索从“属性”页复制的对象 ID，并使用以下查询转到服务主体：
    
      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![用于获取需修改的服务主体的查询](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. 从服务主体对象提取 **appRoles** 属性。 

      ![appRoles 属性的详细信息](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > 如果使用自定义应用（而不是 Azure Marketplace 应用），则会看到两个默认角色：user 和 msiam_access。 对于 Marketplace 应用，msiam_access 是唯一的默认角色。 不需对默认角色进行任何更改。

    h. 为应用程序生成新角色。 

      以下 JSON 是 **appRoles** 对象的示例。 创建类似的对象，以添加应用程序所需的角色。 

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
      ]
      }
      ```
      
      > [!Note]
      > 对于修补操作，只能在 msiam_access 之后添加新角色。 此外，可以根据组织的需要添加任意数量的角色。 Azure AD 会在 SAML 响应中将这些角色的值作为声明值发送。 若要生成与新角色的 ID 相对应的 GUID 值，请使用类似[这样](https://www.guidgenerator.com/)的 Web 工具
    
    i. 返回到 Graph 浏览器，将方法从 **GET** 更改为 **PATCH**。 通过更新 **appRoles** 属性（类似于前述示例中所示的属性）来修补服务主体对象以获取所需的角色。 选择“运行查询”以执行此修补操作。 一条成功消息会确认角色已创建。

      ![包含成功消息的修补操作](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. 在使用更多角色修补服务主体后，可以将用户分配到相应的角色。 若要分配用户，可先转到门户，然后浏览到应用程序。 选择“用户和组”选项卡。此选项卡会列出已分配到应用的所有用户和组。 可以在新角色上添加新用户。 还可以选择现有用户，然后再选择“编辑”来更改角色。

    ![“用户和组”选项卡](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    若要将角色分配给任意用户，请选择新角色，然后选择页面底部的“分配”按钮。

    ![“编辑分配”窗格和“选择角色”窗格](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > 需要在 Azure 门户中刷新会话才能看到新角色。

8. 更新“属性”表以定义角色声明的自定义映射。

9. 在“单一登录”对话框的“用户属性”部分，按图中所示配置 SAML 令牌属性，然后执行以下步骤。
    
    | 属性名称 | 属性值 |
    | -------------- | ----------------|    
    | 角色名称      | user.assignedrole |

    a. 选择“添加属性”，打开“添加属性”窗格。

      ![“添加属性”按钮](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![“添加属性”窗格](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. 在“名称”框中，根据需要键入属性名称。 此示例中使用**角色名称**作为声明名称。

    c. 在“值”列表中，选择为该行显示的属性值。

    d. 将“命名空间”框留空。
    
    e. 选择“确定”。

10. 若要在标识提供者启动的单一登录中测试应用程序，请登录到[访问面板](https://myapps.microsoft.com)，然后选择应用程序磁贴。 在 SAML 令牌中，应该会看到具有所提供的声明名称的用户的所有已分配角色。

## <a name="update-an-existing-role"></a>更新现有角色

若要更新现有角色，请执行以下步骤：

1. 打开 [Azure AD Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)。

2. 使用租户的全局管理员或共同管理员凭据登录到 Graph 浏览器站点。
    
3. 将版本更改为“beta”，然后使用以下查询从租户中提取服务主体列表：
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    如果使用的是多个目录，则请遵循此模式：`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Graph 浏览器对话框，包含用于提取服务主体的查询](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. 从提取的服务主体的列表中，获取需要修改的那一个。 还可使用 Ctrl+F 从列出的所有服务主体中搜索应用程序。 搜索从“属性”页复制的对象 ID，并使用以下查询转到服务主体：
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![用于获取需修改的服务主体的查询](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. 从服务主体对象提取 **appRoles** 属性。
    
    ![appRoles 属性的详细信息](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. 若要更新现有角色，请使用以下步骤。

    ![“PATCH”的请求正文，突出显示了“description”和“displayname”](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    a. 将方法从“GET”更改为“PATCH”。

    b. 复制现有角色，并将其粘贴到“请求正文”下。

    c. 根据需要更新角色的值，具体说来就是更新角色说明、角色值或角色显示名称。

    d. 更新所有要求的角色后，选择“运行查询”。
        
## <a name="delete-an-existing-role"></a>删除现有角色

若要删除现有角色，请执行以下步骤：

1. 在另一个窗口中打开 [Azure AD Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)。

2. 使用租户的全局管理员或共同管理员凭据登录到 Graph 浏览器站点。

3. 将版本更改为“beta”，然后使用以下查询从租户中提取服务主体列表：
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    如果使用的是多个目录，则请遵循此模式：`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph 浏览器对话框，包含用于提取服务主体列表的查询](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. 从提取的服务主体的列表中，获取需要修改的那一个。 还可使用 Ctrl+F 从列出的所有服务主体中搜索应用程序。 搜索从“属性”页复制的对象 ID，并使用以下查询转到服务主体：
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![用于获取需修改的服务主体的查询](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. 从服务主体对象提取 **appRoles** 属性。
    
    ![服务主体对象中 appRoles 属性的详细信息](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. 若要删除现有角色，请使用以下步骤。

    ![“PATCH”的请求正文，IsEnabled 设置为 false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. 将方法从“GET”更改为“PATCH”。

    b. 从应用程序中复制现有角色，并将其粘贴到“请求正文”下。
        
    c. 将想要删除的角色的“IsEnabled”值设置为“false”。

    d. 选择“运行查询”。
    
    > [!NOTE] 
    > 确保拥有 msiam_access 角色且 ID 在生成的角色中匹配。
    
7. 在禁用角色后，从 **appRoles** 节删除该角色块。 将方法保留为 **PATCH**，然后选择“运行查询”。
    
8. 运行查询之后，角色就会被删除。
    
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
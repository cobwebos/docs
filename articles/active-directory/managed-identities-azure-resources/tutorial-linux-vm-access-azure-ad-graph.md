---
title: 使用 Linux VM 系统分配的托管标识访问 Azure AD 图形 API
description: 本教程将指导你完成使用 Linux VM 系统分配的托管标识访问 Azure AD 图形 API 的过程。
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: daveba
ms.openlocfilehash: 25aca9ebe0e5bb618960f8526c4ca3e413ecf6bf
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432636"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>教程：使用 Linux VM 系统分配的托管标识访问 Azure AD 图形 API

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

本教程介绍如何使用 Linux 虚拟机 (VM) 系统分配的托管标识访问 Azure AD 图形 API，以检索其组成员身份。 Azure 资源的托管标识由 Azure 自动管理，可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中插入凭据了。  

在本教程中，我们将在 Azure AD 组中查询 VM 标识的成员身份。 授权决策中经常会使用组信息。 在幕后，VM 的托管标识由 Azure AD 中的**服务主体**表示。 

> [!div class="checklist"]
> * 连接到 Azure AD
> * 将 VM 标识添加到 Azure AD 中的某个组 
> * 向 VM 标识授予 Azure AD Graph 的访问权限 
> * 使用 VM 标识获取访问令牌，并使用该令牌调用 Azure AD Graph

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [安装最新版本的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)

- 向 VM 标识授予 Azure AD Graph 访问权限的帐户需在 Azure AD 中分配有“全局管理员”角色。

## <a name="connect-to-azure-ad"></a>连接到 Azure AD

需要连接到 Azure AD 才能将 VM 分配到组，以及授予 VM 检索其组成员身份的权限。

```cli
az login
```

## <a name="add-your-vms-identity-to-a-group-in-azure-ad"></a>将 VM 的标识添加到 Azure AD 中的某个组

在 Linux VM 上启用系统分配的托管标识时，已在 Azure AD 中创建了一个服务主体。  需将该 VM 添加到组中。 请参阅以下文章，获取有关将 VM 添加到 Azure AD 中的组的说明：

- [添加组成员](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)

## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>授予 VM 对 Azure AD 图形 API 的访问权限

使用 Azure 资源的托管标识，代码可以获取访问令牌，对支持 Azure AD 身份验证的资源进行身份验证。 Microsoft Azure AD 图形 API 支持 Azure AD 身份验证。 此步骤向 VM 标识的服务主体授予 Azure AD Graph 访问权限，以便可以查询组成员身份。 通过**应用程序权限**向服务主体授予 Microsoft 或 Azure AD Graph 的访问权限。 需要授予的应用程序权限类型取决于要在 MS 或 Azure AD Graph 中访问的实体。

在本教程中，我们将使用 `Directory.Read.All` 应用程序权限授予 VM 标识查询组成员身份的能力。 若要授予此权限，需要一个在 Azure AD 中分配有“全局管理员”角色的用户帐户。 通常，我们通过在 Azure 门户中访问应用程序的注册并在注册中添加权限，来向应用程序授予权限。 但是，Azure 资源的托管标识不会在 Azure AD 中注册应用程序对象，而只会注册服务主体。 若要注册应用程序权限，应使用 Azure AD PowerShell 命令行工具。 

Azure AD Graph：
- 服务主体 appId（为应用授予权限时使用）：00000002-0000-0000-c000-000000000000
- 资源 ID（从 Azure 资源的托管标识请求访问令牌时使用）： https://graph.windows.net
- 权限范围参考：[Azure AD Graph 权限参考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-curl"></a>使用 CURL 为应用程序授予权限

1. 检索用于发出 CURL 请求的令牌：

   ```cli
   az account get-access-token --resource "https://graph.windows.net/"
   ```

2. 需要检索并记下 VM 的 `objectId`。 在后续步骤中，将使用此 ID 向 VM 授予读取其组成员身份的权限。 请将 `<ACCESS TOKEN>` 替换为在上一步骤中检索到的访问令牌。

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals?$filter=startswith%28displayName%2C%27myVM%27%29&api-version=1.6' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

3. 使用 Azure AD Graph appID 00000002-0000-0000-c000-000000000000 检索并记录 `odata.type: Microsoft.DirectoryServices.ServicePrincipal` 的 `objectId`，以及 `Directory.Read.All` 应用角色权限的 `id`。  请将 `<ACCESS TOKEN>` 替换为前面检索到的访问令牌。

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals?api-version=1.6&%24filter=appId%20eq%20'00000002-0000-0000-c000-000000000000'" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   响应：

   ```json
   "odata.metadata":"https://graph.windows.net/myorganization/$metadata#directoryObjects",
   "value":[
      {
         "odata.type":"Microsoft.DirectoryServices.ServicePrincipal",
         "objectType":"ServicePrincipal",
         "objectId":"81789304-ff96-402b-ae73-07ec0db26721",
         "deletionTimestamp":null,
         "accountEnabled":true,
         "addIns":[
         ],
         "alternativeNames":[
         ],
         "appDisplayName":"Windows Azure Active Directory",
         "appId":"00000002-0000-0000-c000-000000000000",
         "appOwnerTenantId":"f8cdef31-a31e-4b4a-93e4-5f571e91255a",
         "appRoleAssignmentRequired":false,
         "appRoles":[
            {
               "allowedMemberTypes":[
                  "Application"
               ],
               "description":"Allows the app to read data in your company or school directory, such as users, groups, and apps.",
               "displayName":"Read directory data",
               "id":"5778995a-e1bf-45b8-affa-663a9f3f4d04",
               "isEnabled":true,
               "value":"Directory.Read.All"
            },
            {
               //other appRoles values
            }
   ``` 

4. 现在，使用 Azure AD 图形 API 向 VM 的服务主体授予 Azure AD 目录对象的读取访问权限。  `id` 值是 `Directory.Read.All` 应用角色权限的值，`resourceId` 是服务主体 `odata.type:Microsoft.DirectoryServices.ServicePrincipal` 的 `objectId`（在前一步骤中记下的值）。

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals/<VM Object ID>/appRoleAssignments?api-version=1.6" -X POST -d '{"id":"5778995a-e1bf-45b8-affa-663a9f3f4d04","principalId":"<VM Object ID>","resourceId":"81789304-ff96-402b-ae73-07ec0db26721"}'-H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ``` 
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>使用 VM 的标识获取访问令牌，以调用 Azure AD Graph 

若要完成这些步骤，需要使用 SSH 客户端。 如果使用的是 Windows，可以在[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/about)中使用 SSH 客户端。 如果需要有关配置 SSH 客户端密钥的帮助，请参阅[如何在 Azure 上将 SSH 密钥与 Windows 配合使用](../../virtual-machines/linux/ssh-from-windows.md)或[如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥和私钥对](../../virtual-machines/linux/mac-create-ssh-keys.md)。

1. 在门户中，转到 Linux VM，并单击“概述”中的“连接”。  
2. 使用所选的 SSH 客户端连接到 VM。 
3. 在终端窗口中，使用 CURL 向 Azure 资源终结点的本地托管标识发出请求，以获取 Azure AD Graph 的访问令牌。  
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -H Metadata:true
   ```    
   响应包含访问 Azure AD Graph 时所需的访问令牌。

   响应：

   ```bash
   {
       "access_token":"eyJ0eXAiOiJKV...",
       "expires_in":"3599",
       "expires_on":"1519622535",
       "not_before":"1519618635",
       "resource":"https://graph.windows.net",
       "token_type":"Bearer"
   }
   ```

4. 使用 VM 服务主体的对象 ID（在前面步骤中检索到的值）可以查询 Azure AD 图形 API 来检索该 VM 的组成员身份。 请将 `<OBJECT-ID>` 替换为VM 服务主体的对象 ID，将 `<ACCESS-TOKEN>` 替换为前面获取的访问令牌：

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals/<OBJECT-ID>/getMemberGroups?api-version=1.6' -X POST -d "{\"securityEnabledOnly\": false}" -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS-TOKEN>"
   ```

   响应：

   ```bash   
   Content : {"odata.metadata":"https://graph.windows.net/myorganization/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>后续步骤

在本教程中，我们已学习了如何使用 Linux VM 系统分配的托管标识来访问 Azure AD Graph。  若要详细了解 Azure AD Graph，请参阅：

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)

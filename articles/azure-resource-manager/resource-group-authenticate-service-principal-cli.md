---
title: "通过 Azure CLI 为 Azure 应用创建标识 | Microsoft Docs"
description: "介绍如何使用 Azure CLI 创建 Azure Active Directory 应用程序和服务主体，并通过基于角色的访问控制授予其对资源的访问权限。 它演示如何使用密码或证书对应用程序进行身份验证。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c224a189-dd28-4801-b3e3-26991b0eb24d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/15/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: 3c5826d58887ff1af4df8e66999d9c1a1643bcc7
ms.contentlocale: zh-cn
ms.lasthandoff: 07/21/2017

---
# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>使用 Azure CLI 创建服务主体来访问资源

当有应用或脚本需访问资源时，可以为应用设置一个标识，并使用其自己的凭据对应用进行身份验证。 此标识称为服务主体。 使用此方法能够：

* 将权限分配给应用标识，这些权限不同于自己的权限。 通常情况下，这些权限仅限于应用需执行的操作。
* 执行无人参与的脚本时，使用证书进行身份验证。

本文介绍如何使用 [Azure CLI 1.0](../cli-install-nodejs.md) 对应用程序进行设置，使之能够使用自己的凭据和标识运行。 安装最新版本的 [Azure CLI 1.0](../cli-install-nodejs.md) 以确保环境满足本文中的示例。

## <a name="required-permissions"></a>所需的权限
若要完成本主题，必须在 Azure Active Directory 和 Azure 订阅中均具有足够的权限。 具体而言，必须能够在 Azure Active Directory 中创建应用并向角色分配服务主体。 

检查帐户是否有足够权限的最简方法是使用门户。 请参阅[在门户中检查所需的权限](resource-group-create-service-principal-portal.md#required-permissions)。

现在转到[密码](#create-service-principal-with-password)或[证书](#create-service-principal-with-certificate)身份验证部分。

## <a name="create-service-principal-with-password"></a>使用密码创建服务主体
在本部分中，会执行以下步骤，创建包含密码的 AD 应用程序并“读取者”角色分配到服务主体。

1. 登录到帐户。
   
   ```azurecli
   azure login
   ```
2. 若要创建应用标识，请提供应用名称和密码，如以下命令中所示：
     
   ```azurecli
   azure ad sp create -n exampleapp -p {your-password}
   ```
     
   将返回新的服务主体。 授权时需要使用对象 ID。 登录时需要使用随“服务主体名称”一起列出的 GUID。 此 GUID 的值与应用 ID 相同。在示例应用程序中，此值称为 `Client ID`。 
     
   ```azurecli
   info:    Executing command ad sp create
     
   Creating application exampleapp
     / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
     data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
     data:    Display Name:            exampleapp
     data:    Service Principal Names:
     data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
     data:                             https://www.contoso.org/example
     info:    ad sp create command OK
   ```

3. 向服务主体授予对订阅的权限。 在此示例中，向“读取者”角色（授予读取订阅中所有资源的权限）添加服务主体。 对于其他角色，请参阅 [RBAC：内置角色](../active-directory/role-based-access-built-in-roles.md)。 对于 objectid 参数，请提供创建应用程序时使用的对象 ID。 运行此命令之前，必须留出一些时间让新服务主体传遍 Azure Active Directory。 手动运行这些命令时，任务之间通常已经过足够的时间。 在脚本中，应在命令间添加休眠步骤（如 `sleep 15`）。 如果看到错误称主体不存在于目录中，请重新运行该命令。
   
   ```azurecli
   azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/
   ```
   
就这么简单！ AD 应用程序和服务主体设置完毕。 下一部分演示如何通过 Azure CLI 使用凭据进行登录。 如果想在代码应用程序中使用凭据，则不需要继续了解本主题。 可以跳到[示例应用程序](#sample-applications)，获取使用应用程序 ID 和密码登录的示例。 

### <a name="provide-credentials-through-azure-cli"></a>通过 Azure CLI 提供凭据
现在，需要以应用程序方式登录以执行相应操作。

1. 以服务主体方式登录时，需提供 AD 应用所在目录的租户 ID。 租户是 Azure Active Directory 的实例。 若要检索当前已经过身份验证的订阅的租户 ID，请使用：
   
   ```azurecli
   azure account show
   ```
   
   将返回：
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
     如果需要获取另一个订阅的租户 ID，请使用以下命令：
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. 如果需要检索用于登录的客户端 ID，请使用：
   
   ```azurecli
   azure ad sp show -c exampleapp --json
   ```
   
     用于登录的值是服务主体名称中列出的 GUID。
   
   ```azurecli
   [
     {
       "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "7132aca4-1bdb-4238-ad81-996ff91d8db4"
       ]
     }
   ]
   ```
3. 以服务主体身份登录。
   
   ```azurecli
   azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}
   ```
   
    系统会提示输入密码。 提供在创建 AD 应用程序时指定的密码。
   
   ```azurecli
   info:    Executing command login
   Password: ********
   ```

现在，用户已作为所创建服务主体的服务主体进行身份验证。

也可从要登录的命令行调用 REST 操作。 可以从身份验证响应中检索访问令牌，将其用于其他操作。 若要通过示例来了解如何通过调用 REST 操作来检索访问令牌，请参阅[生成访问令牌](resource-manager-rest-api.md#generating-an-access-token)。

## <a name="create-service-principal-with-certificate"></a>使用证书创建服务主体
执行本部分中的步骤，以便：

* 创建自签名证书
* 使用证书创建 AD 应用程序，并创建服务主体
* 向服务主体分配“读取者”角色

若要完成这些步骤，必须已安装 [OpenSSL](http://www.openssl.org/)。

1. 创建自签名证书。
   
   ```
   openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'
   ```

2. 上一步创建了两个文件 - privkey.pem 和 cert.pem。 将公钥和私钥组合在一个文件中。

   ```
   cat privkey.pem cert.pem > examplecert.pem
   ```

3. 打开 **examplecert.pem** 文件并找到 **-----BEGIN CERTIFICATE-----** 与 **-----END CERTIFICATE-----** 之间的长字符序列。 复制证书数据。 创建服务主体时将此数据作为参数传递。

4. 登录到帐户。

   ```azurecli
   azure login
   ```
5. 若要创建服务主体，请提供应用名称和证书数据，如以下命令中所示：
     
   ```azurecli
   azure ad sp create -n exampleapp --cert-value {certificate data}
   ```
     
   将返回新的服务主体。 授权时需要使用对象 ID。 登录时需要使用随“服务主体名称”一起列出的 GUID。 此 GUID 的值与应用 ID 相同。在示例应用程序中，此值称为“客户端 ID”。 
     
   ```azurecli
   info:    Executing command ad sp create
     
   Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
     data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
     data:    Display Name:     exampleapp
     data:    Service Principal Names:
     data:                      4fd39843-c338-417d-b549-a545f584a745
     data:                      https://www.contoso.org/example
     info:    ad sp create command OK
   ```
6. 向服务主体授予对订阅的权限。 在此示例中，向“读取者”角色（授予读取订阅中所有资源的权限）添加服务主体。 对于其他角色，请参阅 [RBAC：内置角色](../active-directory/role-based-access-built-in-roles.md)。 对于 objectid 参数，请提供创建应用程序时使用的对象 ID。 运行此命令之前，必须留出一些时间让新服务主体传遍 Azure Active Directory。 手动运行这些命令时，任务之间通常已经过足够的时间。 在脚本中，应在命令间添加休眠步骤（如 `sleep 15`）。 如果看到错误称主体不存在于目录中，请重新运行该命令。
   
   ```azurecli
   azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/
   ```
  
### <a name="provide-certificate-through-automated-azure-cli-script"></a>通过自动执行的 Azure CLI 脚本提供证书
现在，需要以应用程序方式登录以执行相应操作。

1. 以服务主体方式登录时，需提供 AD 应用所在目录的租户 ID。 租户是 Azure Active Directory 的实例。 若要检索当前已经过身份验证的订阅的租户 ID，请使用：
   
   ```azurecli
   azure account show
   ```
   
   将返回：
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
   如果需要获取另一个订阅的租户 ID，请使用以下命令：
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. 若要检索证书指纹并删除不需要的字符，请使用：
   
   ```
   openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
   ```
   
   它返回的指纹值类似于：
   
   ```
   30996D9CE48A0B6E0CD49DBB9A48059BF9355851
   ```
3. 如果需要检索用于登录的客户端 ID，请使用：
   
   ```azurecli
   azure ad sp show -c exampleapp
   ```
   
   用于登录的值是服务主体名称中列出的 GUID。
     
   ```azurecli
   [
     {
       "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "4fd39843-c338-417d-b549-a545f584a745",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "4fd39843-c338-417d-b549-a545f584a745"
       ]
     }
   ]
   ```
4. 以服务主体身份登录。
   
   ```azurecli
   azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}
   ```

现在，已作为所创建 Azure Active Directory 应用程序的服务主体进行身份验证。

## <a name="change-credentials"></a>更改凭据

为了保障安全或由于凭据过期，若要更改 AD 应用的凭据，请使用 `azure ad app set`。

若要更改密码，请使用：

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --password p@ssword
```

若要更改证书值，请使用：

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --cert-value {certificate data}
```

## <a name="debug"></a>调试

创建服务主体时，可能会遇到以下错误：

* “Authentication_Unauthorized”或“在上下文中找不到订阅”。 - 如果帐户不具有在 Azure Active Directory 上注册应用[所需的权限](#required-permissions)，会收到此错误。 通常，当仅 Azure Active Directory 中的管理员用户可注册应用且帐户不是管理员帐户时，会看到此错误。请要求管理员向你分配管理员角色，或让用户能够注册应用。

* 帐户“不具有对作用域‘/subscriptions/{guid}’执行操作‘Microsoft.Authorization/roleAssignments/write’的权限”。 - 当帐户不具有足够权限将角色分配给标识时，会看到此错误。 请要求订阅管理员你将添加到用户访问管理员角色。

## <a name="sample-applications"></a>示例应用程序
有关在不同平台上通过应用程序登录的信息，请参阅：

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>后续步骤
* 有关将应用程序集成到 Azure 以管理资源的详细步骤，请参阅 [Developer's guide to authorization with the Azure Resource Manager API](resource-manager-api-authentication.md)（使用 Azure Resource Manager API 进行授权的开发人员指南）。
* 若要获取有关使用证书和 Azure CLI 的详细信息，请参阅[从 Linux 命令行对 Azure 服务主体进行基于证书的身份验证](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)。 
* 有关可对用户授予或拒绝的可用操作的列表，请参阅 [Azure Resource Manager 资源提供程序操作](../active-directory/role-based-access-control-resource-provider-operations.md)。


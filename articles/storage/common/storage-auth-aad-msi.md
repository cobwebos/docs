---
title: 使用托管标识授予对数据的访问权限
titleSuffix: Azure Storage
description: 使用 Azure 资源的托管标识对 Azure Vm 中运行的应用程序、函数应用和其他应用程序的 blob 和队列数据访问授权。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 2087e5f8ec397123df504e9d30d351a0ba79b4a5
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018750"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>使用 Azure 资源托管标识授予对 Blob 和队列数据的访问权限

Azure Blob 和队列存储支持使用 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)进行 Azure Active Directory (Azure AD) 身份验证。 Azure 资源的托管标识可以从 Azure 虚拟机 (VM)、函数应用、虚拟机规模集和其他服务中运行的应用程序使用 Azure AD 凭据授权对 Blob 和队列数据的访问权限。 将 Azure 资源的托管标识与 Azure AD 身份验证结合使用，可避免将凭据随在云中运行的应用程序一起存储。  

本文介绍如何使用 Azure 资源的托管标识对访问授权，以便对 Azure VM 中的 Blob 或队列数据进行访问， 另外还介绍如何在开发环境中对代码进行测试。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上启用托管标识

在使用 Azure 资源的托管标识对 VM 中 Blob 和队列的访问权限进行授权之前，必须首先在 VM 上启用针对 Azure 资源的托管标识。 若要了解如何为 Azure 资源启用托管标识，请参阅下述文章之一：

- [Azure 门户](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 资源管理器客户端库](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

有关托管标识的详细信息，请参阅 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="authenticate-with-the-azure-identity-library"></a>使用 Azure 标识库进行身份验证

Azure 标识客户端库为 [Azure SDK](https://github.com/Azure/azure-sdk) 提供 Azure Azure AD 令牌身份验证支持。 最新版本的适用于 .NET、Java、Python 和 JavaScript 的 Azure 存储客户端库与 Azure 标识库集成，提供一种简单而安全的方法来获取用于为 Azure 存储请求进行授权的 OAuth 2.0 令牌。

Azure 标识客户端库的优点在于，它使你可以使用相同的代码来验证你的应用程序是在开发环境中运行还是在 Azure 中运行。 用于 .NET 的 Azure 标识客户端库可以对安全主体进行身份验证。 代码在 Azure 中运行时，安全主体是 Azure 资源的托管标识。 在开发环境中，不存在托管标识，因此客户端库出于测试目的会对用户或服务主体进行身份验证。

进行身份验证后，Azure 标识客户端库会获得令牌凭据。 然后，此令牌凭据会封装在服务客户端对象中。该对象由你创建，用于对 Azure 存储执行操作。 库会获取相应的令牌凭据，为你无缝处理这一切。

有关用于 .NET 的 Azure 标识客户端库的详细信息，请参阅[用于 .NET 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)。 有关 Azure 标识客户端库的参考文档，请参阅 [Azure.Identity 命名空间](/dotnet/api/azure.identity)。

### <a name="assign-azure-roles-for-access-to-data"></a>分配 Azure 角色以访问数据

当 Azure AD 安全主体尝试访问 Blob 或队列数据时，该安全主体必须有资源访问权限。 无论安全主体是 Azure 中的托管标识还是在开发环境中运行代码的 Azure AD 用户帐户，都必须为安全主体分配 Azure 角色，以授予对 Azure 存储中的 blob 或队列数据的访问权限。 有关通过 RBAC 分配权限的信息，请参阅[使用 Azure Active Directory 授予对 azure blob 和队列的访问](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights)权限中的 "为**访问权限分配 azure 角色**" 一节。

### <a name="authenticate-the-user-in-the-development-environment"></a>在开发环境中对用户进行身份验证

代码在开发环境中运行时，可能会自动处理身份验证，也可能需要浏览器登录才能进行身份验证，具体取决于使用哪些工具。 例如，Microsoft Visual Studio 支持单一登录 (SSO) ，使活动 Azure AD 用户帐户自动用于身份验证。 有关 SSO 的详细信息，请参阅 [对应用程序的单一登录](../../active-directory/manage-apps/what-is-single-sign-on.md)。

其他开发工具可能会提示你通过 Web 浏览器登录。

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>在开发环境中对服务主体进行身份验证

如果开发环境不支持单一登录或通过 Web 浏览器登录，可以使用服务主体从开发环境进行身份验证。

#### <a name="create-the-service-principal"></a>创建服务主体

若要创建具有 Azure CLI 的服务主体并分配 Azure 角色，请调用 [az ad sp create for rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) 命令。 提供要分配给新服务主体的 Azure 存储数据访问角色。 此外，请提供角色分配的范围。 有关为 Azure 存储提供的内置角色的详细信息，请参阅 [azure 内置角色](../../role-based-access-control/built-in-roles.md)。

如果没有足够的权限将角色分配给服务主体，可能需要请求帐户所有者或管理员来执行相关角色分配。

下面的示例使用 Azure CLI 创建新服务主体，并将帐户范围内的“存储 Blob 数据读取者”角色分配给它****

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

`az ad sp create-for-rbac` 命令返回 JSON 格式的服务主体属性列表。 复制这些值，以便在下一步中使用它们来创建必要的环境变量。

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Azure 角色分配可能需要几分钟时间来进行传播。

#### <a name="set-environment-variables"></a>设置环境变量

Azure 标识客户端库会在运行时读取三个环境变量中的值，以对服务主体进行身份验证。 下表介绍了为每个环境变量设置的值。

|环境变量|“值”
|-|-
|`AZURE_CLIENT_ID`|服务主体的应用 ID
|`AZURE_TENANT_ID`|服务主体的 Azure AD 租户 ID
|`AZURE_CLIENT_SECRET`|为服务主体生成的密码

> [!IMPORTANT]
> 设置环境变量后，关闭并重新打开控制台窗口。 如果使用的是 Visual Studio 或其他开发环境，则可能需要重新启动开发环境以便环境能够注册新的环境变量。

有关详细信息，请参阅[在门户中创建 Azure 应用标识](../../active-directory/develop/howto-create-service-principal-portal.md)。

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 代码示例：创建块 Blob

向代码添加以下 `using` 指令，以便使用 Azure 标识和 Azure 存储客户端库。

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

若要获取令牌凭据，以便代码用它来授权对 Azure 存储的请求，请创建 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 类的实例。 以下代码示例演示了如何获取经身份验证的令牌凭据并使用它来创建服务客户端对象，然后使用服务客户端来上传新的 Blob：

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> 若要使用 Azure AD 授权针对 Blob 或队列数据的请求，必须对这些请求使用 HTTPS。

## <a name="next-steps"></a>后续步骤

- [使用 RBAC 管理对存储数据的访问权限](storage-auth-aad-rbac.md)。
- [将 Azure AD 与存储应用程序一起使用](storage-auth-aad-app.md)。
- [使用 Azure AD 凭据运行 Azure CLI 或 PowerShell 命令以访问 Blob 或队列数据](authorize-active-directory-powershell.md)。

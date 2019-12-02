---
title: 使用 Azure 资源的 Azure Active Directory 和托管标识授予对 blob 和队列的访问权限-Azure 存储
description: Azure Blob 和队列存储支持使用 Azure 资源的 Azure Active Directory 和托管标识授权访问资源。 可以使用 Azure 资源的托管标识，通过 Azure 虚拟机中运行的应用程序、函数应用、虚拟机规模集和其他资源来授予对 blob 和队列的访问权限。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 3e24cb2d4b5b82f6878647cdd631bd8ebca16199
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666149"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>使用 Azure 资源的 Azure Active Directory 和托管标识授予对 blob 和队列的访问权限

Azure Blob 和队列存储支持使用 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)进行 Azure Active Directory (Azure AD) 身份验证。 Azure 资源的托管标识可以使用 Azure 虚拟机（Vm）、函数应用、虚拟机规模集和其他服务中运行的应用程序的 Azure AD 凭据，授权对 blob 和队列数据的访问。 通过将 Azure 资源的托管标识与 Azure AD authentication 一起使用，你可以避免将凭据存储在云中运行的应用程序。  

本文介绍如何使用 Azure 资源的托管标识，授权从 Azure VM 访问 blob 或队列数据。 还介绍了如何在开发环境中测试代码。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上启用托管标识

必须先为 VM 上的 Azure 资源启用托管标识，才能使用 Azure 资源的托管标识授予对 VM 中的 blob 和队列的访问权限。 若要了解如何为 Azure 资源启用托管标识，请参阅下述文章之一：

- [Azure 门户](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 资源管理器客户端库](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

有关托管标识的详细信息，请参阅[Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="authenticate-with-the-azure-identity-library"></a>通过 Azure 标识库进行身份验证

Azure 标识客户端库的优点在于，它使你可以使用相同的代码来验证你的应用程序是在开发环境中运行还是在 Azure 中运行。 在 Azure 环境中运行的代码中，客户端库对 Azure 资源的托管标识进行身份验证。 在开发环境中，托管标识不存在，因此，客户端库将对用户或服务主体进行身份验证，以便进行测试。

适用于 .NET 的 Azure 标识客户端库对安全主体进行身份验证。 当你的代码在 Azure 中运行时，安全主体是 Azure 资源的托管标识。

进行身份验证后，Azure 标识客户端库将获取令牌凭据。 然后，在创建的服务客户端对象中封装此令牌凭据，以对 Azure 存储空间执行操作。 库通过获取适当的令牌凭据，无缝地处理这种情况。

有关 Azure 标识客户端库的详细信息，请参阅[适用于 .net 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)。

### <a name="assign-role-based-access-control-rbac-roles-for-access-to-data"></a>分配基于角色的访问控制（RBAC）角色以访问数据

当 Azure AD 安全主体尝试访问 blob 或队列数据时，该安全主体必须具有对资源的权限。 无论安全主体是 Azure 中的托管标识还是在开发环境中运行代码的 Azure AD 用户帐户，都必须为安全主体分配允许访问 Azure 存储中的 blob 或队列数据的 RBAC 角色。 有关通过 RBAC 分配权限的信息，请参阅[使用 Azure Active Directory 授予对 Azure blob 和队列的访问](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)权限中标题为**访问权限分配 RBAC 角色**的部分。

### <a name="authenticate-the-user-in-the-development-environment"></a>在开发环境中对用户进行身份验证

当代码在开发环境中运行时，可以自动处理身份验证，或者可能需要浏览器登录，具体取决于所使用的工具。 Microsoft Visual Studio 支持单一登录（SSO），以便 active Azure AD 用户帐户自动用于身份验证。 有关 SSO 的详细信息，请参阅[对应用程序的单一登录](../../active-directory/manage-apps/what-is-single-sign-on.md)。

其他开发工具可能会提示您通过 web 浏览器登录。

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>对开发环境中的服务主体进行身份验证

如果开发环境不支持单一登录或通过 web 浏览器登录，则可以使用服务主体从开发环境进行身份验证。

#### <a name="create-the-service-principal"></a>创建服务主体

若要创建具有 Azure CLI 的服务主体并分配一个 RBAC 角色，请调用[az ad sp create for rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)命令。 提供要分配给新服务主体的 Azure 存储数据访问角色。 此外，请提供角色分配的作用域。 有关为 Azure 存储提供的内置角色的详细信息，请参阅[azure 资源的内置角色](../../role-based-access-control/built-in-roles.md)。

如果你没有足够的权限将角色分配给服务主体，你可能需要请求帐户所有者或管理员执行角色分配。

下面的示例使用 Azure CLI 创建新服务主体，并使用帐户范围将**存储 Blob 数据读取器**角色分配给它

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
> RBAC 角色分配可能需要几分钟才能传播。

#### <a name="set-environment-variables"></a>设置环境变量。

Azure 标识客户端库会在运行时读取三个环境变量中的值，以对服务主体进行身份验证。 下表介绍了为每个环境变量设置的值。

|环境变量|Value
|-|-
|`AZURE_CLIENT_ID`|服务主体的应用 ID
|`AZURE_TENANT_ID`|服务主体的 Azure AD 租户 ID
|`AZURE_CLIENT_SECRET`|为服务主体生成的密码

> [!IMPORTANT]
> 设置环境变量后，请关闭并重新打开控制台窗口。 如果你使用的是 Visual Studio 或其他开发环境，则可能需要重新启动开发环境才能注册新的环境变量。

有关详细信息，请参阅[在门户中为 Azure 应用创建标识](../../active-directory/develop/howto-create-service-principal-portal.md)。

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 代码示例：创建块 blob

将以下 `using` 指令添加到你的代码，以使用 Azure 标识和 Azure 存储客户端库。

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

若要获取代码可用于向 Azure 存储授权请求的令牌凭据，请创建[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)类的实例。 下面的代码示例演示如何获取经过身份验证的令牌凭据，并使用它来创建服务客户端对象，然后使用服务客户端上传新的 blob：

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
> 若要使用 Azure AD 对 blob 或队列数据的请求进行授权，必须对这些请求使用 HTTPS。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 存储的 RBAC 角色的详细信息，请参阅[使用 Rbac 管理对存储数据的访问权限](storage-auth-aad-rbac.md)。
- 若要了解如何从存储应用程序内授予容器和队列访问权限，请参阅[将 Azure AD 与存储应用程序配合使用](storage-auth-aad-app.md)。
- 若要了解如何使用 Azure AD 凭据运行 Azure CLI 和 PowerShell 命令，请参阅[使用 Azure AD 凭据运行 Azure CLI 或 powershell 命令以访问 blob 或队列数据](storage-auth-aad-script.md)。

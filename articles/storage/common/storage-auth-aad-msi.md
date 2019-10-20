---
title: 使用 Azure 资源的 Azure Active Directory 和托管标识授予对 blob 和队列的访问权限-Azure 存储
description: Azure Blob 和队列存储支持使用 Azure 资源的 Azure Active Directory 和托管标识授权访问资源。 可以使用 Azure 资源的托管标识，通过 Azure 虚拟机中运行的应用程序、函数应用、虚拟机规模集和其他资源来授予对 blob 和队列的访问权限。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 833aa7dcce5c429b3005a378e93e2177df1eb0d4
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595178"
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

## <a name="authenticate-with-the-azure-identity-library-preview"></a>用 Azure 标识库进行身份验证（预览版）

适用于 .NET 的 Azure 标识客户端库（预览版）对安全主体进行身份验证。 当你的代码在 Azure 中运行时，安全主体是 Azure 资源的托管标识。

当代码在开发环境中运行时，可以自动处理身份验证，或者可能需要浏览器登录，具体取决于所使用的工具。 Microsoft Visual Studio 支持单一登录（SSO），以便 active Azure AD 用户帐户自动用于身份验证。 有关 SSO 的详细信息，请参阅[对应用程序的单一登录](../../active-directory/manage-apps/what-is-single-sign-on.md)。

其他开发工具可能会提示您通过 web 浏览器登录。 你还可以使用服务主体从开发环境进行身份验证。 有关详细信息，请参阅[在门户中为 Azure 应用创建标识](../../active-directory/develop/howto-create-service-principal-portal.md)。

进行身份验证后，Azure 标识客户端库将获取令牌凭据。 然后，在创建的服务客户端对象中封装此令牌凭据，以对 Azure 存储空间执行操作。 库通过获取适当的令牌凭据，无缝地处理这种情况。

有关 Azure 标识客户端库的详细信息，请参阅[适用于 .net 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)。

## <a name="assign-rbac-roles-for-access-to-data"></a>为访问数据分配 RBAC 角色

当 Azure AD 安全主体尝试访问 blob 或队列数据时，该安全主体必须具有对资源的权限。 无论安全主体是 Azure 中的托管标识还是在开发环境中运行代码的 Azure AD 用户帐户，都必须为安全主体分配允许访问 Azure 存储中的 blob 或队列数据的 RBAC 角色。 有关通过 RBAC 分配权限的信息，请参阅[使用 Azure Active Directory 授予对 Azure blob 和队列的访问](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)权限中标题为**访问权限分配 RBAC 角色**的部分。

## <a name="install-the-preview-packages"></a>安装预览包

本文中的示例使用适用于 Blob 存储的 Azure 存储客户端库的最新预览版本。 若要安装预览版包，请从 NuGet 包管理器控制台运行以下命令：

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

本文中的示例还使用[适用于 .net 的 Azure 标识客户端库](https://www.nuget.org/packages/Azure.Identity/)的最新预览版本通过 Azure AD 凭据进行身份验证。 若要安装预览版包，请从 NuGet 包管理器控制台运行以下命令：

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="net-code-example-create-a-block-blob"></a>.NET 代码示例：创建块 blob

将以下 `using` 指令添加到你的代码，以使用 Azure 标识和 Azure 存储客户端库的预览版本。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
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
    catch (StorageRequestFailedException e)
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

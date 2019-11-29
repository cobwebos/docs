---
title: 静态数据的 Azure 存储加密 | Microsoft Docs
description: Azure 存储在将数据保存到云之前会自动对其进行加密，以此保护数据。 您可以依赖于 Microsoft 托管的密钥来加密您的存储帐户，也可以通过自己的密钥来管理加密。
services: storage
author: tamram
ms.service: storage
ms.date: 10/02/2019
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: cfac7fdbbdbf06ae74385fbc33e61d11cb99ff87
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74066320"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>静态数据的 Azure 存储加密

Azure 存储在将数据保存到云时会自动加密数据。 加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。 Azure 存储中的数据将使用 256 位 [AES 加密法](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最强大块加密法之一）以透明方式进行加密和解密，并符合 FIPS 140-2 规范。 Azure 存储加密法类似于 Windows 上的 BitLocker 加密法。

为所有新的存储帐户启用了 Azure 存储加密，因此无法禁用。 由于数据默认受到保护，因此无需修改代码或应用程序，即可利用 Azure 存储加密。

不管存储帐户的性能层（标准或高级）或部署模型（Azure 资源管理器或经典）是什么，都会将其加密。 所有 Azure 存储冗余选项都支持加密，存储帐户的所有副本都会加密。 所有 Azure 存储资源（包括 Blob、磁盘、文件、队列和表）都会加密。 所有对象元数据也会加密。

加密不影响 Azure 存储的性能。 Azure 存储加密不会产生额外的费用。

有关 Azure 存储加密中的加密模块的详细信息，请参阅[加密 API：下一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

您可以依赖于 Microsoft 托管的密钥来加密您的存储帐户，也可以通过自己的密钥来管理加密。 如果你选择使用自己的密钥来管理加密，则可以采用两种做法：

- 可以指定客户管理的密钥用于加密和解密存储帐户中的所有数据。 客户管理的密钥用于加密存储帐户中所有服务内的所有数据。
- 可以在 Blob 存储操作中指定客户提供的密钥。 对 Blob 存储发出读取或写入请求的客户端可以在请求中包含加密密钥，以便精细控制 Blob 数据的加密和解密方式。

下表比较了 Azure 存储加密的密钥管理选项。

|                                        |    Microsoft 管理的密钥                             |    客户管理的密钥                                                                                                                        |    客户提供的密钥                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    加密/解密操作    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    支持的 Azure 存储服务    |    All                                                |    Blob 存储、Azure 文件存储                                                                                                               |    Blob 存储                                                                  |
|    密钥存储                         |    Microsoft 密钥存储    |    Azure Key Vault                                                                                                                              |    Azure Key Vault 或任何其他密钥存储                                                                 |
|    密钥轮换责任         |    Microsoft                                          |    Customer                                                                                                                                     |    Customer                                                                      |
|    密钥使用情况                           |    Microsoft                                          |    Azure 门户、存储资源提供程序 REST API、Azure 存储管理库、PowerShell、CLI        |    Azure 存储 REST API（Blob 存储）、Azure 存储客户端库    |
|    密钥访问权限                          |    仅限 Microsoft                                     |    Microsoft、客户                                                                                                                    |    仅限客户                                                                 |

以下部分更详细地介绍了每个密钥管理选项。

## <a name="microsoft-managed-keys"></a>Microsoft 管理的密钥

默认情况下，存储帐户使用 Microsoft 托管的加密密钥。 可以在 **Azure 门户**的“加密”部分查看存储帐户的加密设置，如下图所示。[](https://portal.azure.com)

![查看使用 Microsoft 托管密钥加密的帐户](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys"></a>客户管理的密钥

可以选择使用自己的密钥在存储帐户级别管理 Azure 存储加密。 在存储帐户级别指定客户管理的密钥时，该密钥将用于加密和解密存储帐户中的所有数据，包括 Blob、队列、文件和表数据。  使用客户管理的密钥可以更灵活地创建、轮换、禁用和撤销访问控制。 还可以审核用于保护数据的加密密钥。

必须使用 Azure Key Vault 来存储客户管理的密钥。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 存储帐户和 Key Vault 必须在同一个区域中，但可以在不同的订阅中。 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](../../key-vault/key-vault-overview.md)。

此图显示了 Azure 存储如何使用 Azure Active Directory 和 Azure Key Vault 通过客户管理的密钥发出请求：

![Azure 存储中客户管理的密钥的工作原理示意图](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

以下列表解释了示意图中带编号的步骤：

1. Azure Key Vault 管理员向与存储帐户关联的托管标识授予对加密密钥的权限。
2. Azure 存储管理员使用存储帐户的客户管理密钥配置加密。
3. Azure 存储使用与存储帐户关联的托管标识，对通过 Azure Active Directory 访问 Azure Key Vault 的活动进行身份验证。
4. Azure 存储使用 Azure Key Vault 中的客户密钥包装帐户加密密钥。
5. 对于读/写操作，Azure 存储将向 Azure Key Vault 发送包装和解包帐户加密密钥的请求，以执行加密和解密操作。

若要撤销对存储帐户中客户管理的密钥的访问权限，请参阅 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) 和 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤销访问权限会实际阻止对存储帐户中所有数据的访问，因为 Azure 存储帐户无法访问加密密钥。

客户托管的密钥也可作为公共预览版提供给 Azure 托管磁盘，而与存储的其余部分相比，客户管理的密钥的工作方式略有不同。 有关详细信息，请参阅[主题中的文章](../../virtual-machines/linux/disk-encryption.md#customer-managed-keys-public-preview)。

若要了解如何将客户管理的密钥与 Azure 存储配合使用，请参阅以下文章之一：

- [通过 Azure 门户配置客户托管密钥用于 Azure 存储加密](storage-encryption-keys-portal.md)
- [通过 PowerShell 配置客户托管密钥用于 Azure 存储加密](storage-encryption-keys-powershell.md)
- [在 Azure CLI 中将客户管理的密钥用于 Azure 存储加密](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> 客户托管密钥依赖于 Azure 资源的托管标识，后者是 Azure Active Directory (Azure AD) 的一项功能。 在 Azure 门户中配置客户管理的密钥时，系统会在幕后自动将一个托管标识分配到你的存储帐户。 如果随后将订阅、资源组或存储帐户从一个 Azure AD 目录移到另一个目录，与存储帐户关联的托管标识不会传输到新租户，因此客户管理的密钥可能不再起作用。 有关详细信息，请参阅 **Azure 资源的常见问题解答和已知问题**中的“在 Azure AD 目录之间转移订阅”[](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。  

## <a name="customer-provided-keys-preview"></a>客户提供的密钥（预览版）

对 Azure Blob 存储发出请求的客户端可以选择在单个请求中提供加密密钥。 在请求中包含加密密钥可以精细控制 Blob 存储操作的加密设置。 客户提供的密钥（预览版）可以存储在 Azure Key Vault 或另一密钥存储中。

### <a name="encrypting-read-and-write-operations"></a>加密读取和写入操作

当客户端应用程序在请求中提供加密密钥时，Azure 存储将在读取和写入 Blob 数据时以透明方式执行加密和解密。 加密密钥的 SHA-256 哈希将连同 Blob 内容一起写入，用于验证针对该 Blob 的所有后续操作是否使用相同的加密密钥。 Azure 存储不会存储或管理客户端连同请求一起发送的加密密钥。 加密或解密过程完成后，会立即以安全方式丢弃该密钥。

当客户端使用客户提供的密钥创建或更新 Blob 时，针对该 Blob 的后续读取和写入请求也必须提供该密钥。 如果在针对已使用客户提供的密钥加密的 Blob 的请求中未提供该密钥，则请求将会失败并返回错误代码 409（冲突）。

如果客户端应用程序在请求上发送加密密钥，并且使用 Microsoft 托管密钥或客户管理的密钥加密存储帐户，则 Azure 存储使用请求中提供的密钥来加密和解密。

若要在请求中发送加密密钥，客户端必须使用 HTTPS 来与 Azure 存储建立安全连接。

每个 Blob 快照可以有自身的加密密钥。

### <a name="request-headers-for-specifying-customer-provided-keys"></a>用于指定客户提供的密钥的请求标头

对于 REST 调用，客户端可以使用以下标头在请求中向 Blob 存储安全传递加密密钥信息：

|请求标头 | 说明 |
|---------------|-------------|
|`x-ms-encryption-key` |对于写入和读取请求都是必需的。 Base64 编码的 AES-256 加密密钥值。 |
|`x-ms-encryption-key-sha256`| 对于写入和读取请求都是必需的。 加密密钥的 Base64 编码 SHA256。 |
|`x-ms-encryption-algorithm` | 对于写入请求是必需的，对于读取请求是可选的。 指定在通过给定密钥加密数据时要使用的算法。 必须是 AES256。 |

在请求中指定加密密钥是可选操作。 但是，如果为写入操作指定上面列出的标头之一，则必须指定所有这些标头。

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>支持客户提供的密钥的 Blob 存储操作

以下 Blob 存储操作支持在请求中发送客户提供的加密密钥：

- [放置 Blob](/rest/api/storageservices/put-blob)
- [放置块列表](/rest/api/storageservices/put-block-list)
- [放置块](/rest/api/storageservices/put-block)
- [从 URL 放置块](/rest/api/storageservices/put-block-from-url)
- [放置页](/rest/api/storageservices/put-page)
- [从 URL 放置页](/rest/api/storageservices/put-page-from-url)
- [追加块](/rest/api/storageservices/append-block)
- [设置 Blob 属性](/rest/api/storageservices/set-blob-properties)
- [设置 Blob 元数据](/rest/api/storageservices/set-blob-metadata)
- [获取 Blob](/rest/api/storageservices/get-blob)
- [获取 Blob 属性](/rest/api/storageservices/get-blob-properties)
- [获取 Blob 元数据](/rest/api/storageservices/get-blob-metadata)
- [快照 Blob](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>轮换客户提供的密钥

若要轮换在请求中传递的加密密钥，请下载 Blob，并使用新的加密密钥重新上传该 Blob。

> [!IMPORTANT]
> 无法使用 Azure 门户来读取或写入通过请求中提供的密钥加密的容器或 Blob。
>
> 请务必在 Azure Key Vault 等安全密钥存储中，保护在对 Blob 存储发出的请求中提供的加密密钥。 如果你尝试在不使用加密密钥的情况下对容器或 Blob 执行写入操作，该操作将会失败，并且你会失去对象访问权限。

### <a name="example-use-a-customer-provided-key-to-upload-a-blob-in-net"></a>示例：使用客户提供的密钥在 .NET 中上传 blob

以下示例创建客户提供的密钥，并使用该密钥上传 Blob。 该代码将上传一个块，然后提交块列表以将 Blob 写入 Azure 存储。 该密钥是通过设置 [CustomerProvidedKey](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) 属性在 [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.customerprovidedkey) 对象中提供的。

该密钥是使用 [AesCryptoServiceProvider](/dotnet/api/system.security.cryptography.aescryptoserviceprovider) 类创建的。 若要在代码中创建此类的实例，请添加引用 `using` 命名空间的 `System.Security.Cryptography` 语句：

```csharp
public static void UploadBlobWithClientKey(CloudBlobContainer container)
{
    // Create a new key using the Advanced Encryption Standard (AES) algorithm.
    AesCryptoServiceProvider keyAes = new AesCryptoServiceProvider();

    // Specify the key as an option on the request.
    BlobCustomerProvidedKey customerProvidedKey = new BlobCustomerProvidedKey(keyAes.Key);
    var options = new BlobRequestOptions
    {
        CustomerProvidedKey = customerProvidedKey
    };

    string blobName = "sample-blob-" + Guid.NewGuid();
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);

    try
    {
        // Create an array of random bytes.
        byte[] buffer = new byte[1024];
        Random rnd = new Random();
        rnd.NextBytes(buffer);

        using (MemoryStream sourceStream = new MemoryStream(buffer))
        {
            // Write the array of random bytes to a block.
            int blockNumber = 1;
            string blockId = Convert.ToBase64String(Encoding.ASCII.GetBytes(string.Format("BlockId{0}",
                blockNumber.ToString("0000000"))));

            // Write the block to Azure Storage.
            blockBlob.PutBlock(blockId, sourceStream, null, null, options, null);

            // Commit the block list to write the blob.
            blockBlob.PutBlockList(new List<string>() { blockId }, null, options, null);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure 存储加密与磁盘加密

Azure 存储加密对备份 Azure 虚拟机磁盘的页 blob 进行了加密。 此外，还可以选择通过[Azure 磁盘加密](../../security/azure-security-disk-encryption-overview.md)对所有 Azure 虚拟机磁盘（包括本地临时磁盘）进行加密。 Azure 磁盘加密使用 Windows 上的行业标准 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 或者 Linux 上的 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 来提供与 Azure Key Vault 集成的基于操作系统的加密解决方案。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 密钥保管库？](../../key-vault/key-vault-overview.md)
- [通过 Azure 门户配置客户托管密钥用于 Azure 存储加密](storage-encryption-keys-portal.md)
- [通过 PowerShell 配置客户托管密钥用于 Azure 存储加密](storage-encryption-keys-powershell.md)
- [通过 Azure CLI 配置客户托管密钥用于 Azure 存储加密](storage-encryption-keys-cli.md)

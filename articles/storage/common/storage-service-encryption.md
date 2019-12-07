---
title: 静态数据的 Azure 存储加密
description: Azure 存储通过在将数据保存到云之前自动对其进行加密来保护数据。 您可以依赖于 Microsoft 托管的密钥来加密您的存储帐户，也可以通过自己的密钥来管理加密。
services: storage
author: tamram
ms.service: storage
ms.date: 12/05/2019
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: a09d2c0c2a393acd4882842dc023b0f5f682e813
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895133"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>静态数据的 Azure 存储加密

将数据保存到云时，Azure 存储会自动对其进行加密。 Azure 存储加密可以保护数据，并帮助你满足组织的安全性和符合性承诺。

## <a name="about-azure-storage-encryption"></a>关于 Azure 存储加密

Azure 存储中的数据以透明方式加密和解密，并使用256位[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)，这是可用的最强大的块密码之一，并且符合 FIPS 140-2。 Azure 存储加密类似于 Windows 上的 BitLocker 加密。

为所有新的存储帐户（包括资源管理器和经典存储帐户）启用 Azure 存储加密。 无法禁用 Azure 存储加密。 因为默认情况下，你的数据是安全的，所以你无需修改代码或应用程序即可利用 Azure 存储加密。

无论存储帐户的性能层（标准或高级）还是部署模型（Azure 资源管理器或经典），都将对其进行加密。 所有 Azure 存储冗余选项都支持加密，并对存储帐户的所有副本进行加密。 所有 Azure 存储资源都进行了加密，包括 blob、磁盘、文件、队列和表。 所有对象元数据也已加密。

加密不会影响 Azure 存储性能。 对于 Azure 存储加密，无需额外付费。

2017年10月20日后写入 Azure 存储的每个块 blob、追加 blob 或页 blob 均已加密。 在此日期之前创建的 blob 继续由后台进程加密。 若要强制加密在2017年10月20日之前创建的 blob，可以重写 blob。 若要了解如何检查 blob 的加密状态，请参阅[检查 blob 的加密状态](../blobs/storage-blob-encryption-status.md)。

有关 Azure 存储加密中的加密模块的详细信息，请参阅[加密 API：下一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

您可以依赖于 Microsoft 托管的密钥来加密您的存储帐户，也可以通过自己的密钥来管理加密。 如果选择使用自己的密钥管理加密，则有两个选项：

- 你可以使用 Azure Key Vault 指定*客户托管的密钥*，以便在 Blob 存储和 Azure 文件中对数据进行加密和解密。
- 可以在 Blob 存储操作上指定*客户提供的密钥*。 对 Blob 存储进行读取或写入请求的客户端可以在请求中包含加密密钥，以精细控制如何对 blob 数据进行加密和解密。

下表比较了 Azure 存储加密的密钥管理选项。

|                                        |    Microsoft 托管的密钥                             |    客户管理的密钥                                                                                                                        |    客户提供的密钥                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    加密/解密操作    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    支持 Azure 存储服务    |    所有                                                |    Blob 存储，Azure 文件                                                                                                               |    Blob 存储                                                                  |
|    密钥存储                         |    Microsoft 密钥存储    |    Azure 密钥保管库                                                                                                                              |    Azure Key Vault 或任何其他密钥存储                                                                 |
|    关键轮换责任         |    Microsoft                                          |    客户                                                                                                                                     |    客户                                                                      |
|    密钥使用情况                           |    Microsoft                                          |    Azure 门户、存储资源提供程序 REST API、Azure 存储管理库、PowerShell、CLI        |    Azure 存储 REST API （Blob 存储）、Azure 存储客户端库    |
|    密钥访问权限                          |    仅限 Microsoft                                     |    Microsoft，客户                                                                                                                    |    仅限客户                                                                 |

以下部分更详细地介绍了密钥管理的每个选项。

## <a name="microsoft-managed-keys"></a>Microsoft 托管的密钥

默认情况下，存储帐户使用 Microsoft 托管的加密密钥。 你可以在[Azure 门户](https://portal.azure.com)的 "**加密**" 部分中查看存储帐户的加密设置，如下图所示。

![查看通过 Microsoft 管理的密钥加密的帐户](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys-with-azure-key-vault"></a>客户托管的密钥与 Azure Key Vault

可以在存储帐户级别用自己的密钥来管理 Azure 存储加密。 在存储帐户级别指定客户托管密钥时，该密钥用于加密和解密存储帐户中的所有 blob 和文件数据。 客户管理的密钥提供更大的灵活性，以创建、轮换、禁用和撤消访问控制。 你还可以审核用于保护数据的加密密钥。

必须使用 Azure Key Vault 来存储客户管理的密钥。 你可以创建自己的密钥并将其存储在密钥保管库中，也可以使用 Azure Key Vault Api 来生成密钥。 存储帐户和 Key Vault 必须在同一个区域中，但可以在不同的订阅中。 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](../../key-vault/key-vault-overview.md)。

此图显示了 Azure 存储如何使用 Azure Active Directory 和 Azure Key Vault 来使用客户管理的密钥发出请求：

![显示客户托管的密钥在 Azure 存储中的工作方式的示意图](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

以下列表说明了图中的编号步骤：

1. Azure Key Vault 管理员向与存储帐户关联的托管标识授予对加密密钥的权限。
2. Azure 存储管理员使用存储帐户的客户托管密钥配置加密。
3. Azure 存储使用与存储帐户关联的托管标识，通过 Azure Active Directory 对 Azure Key Vault 的访问进行身份验证。
4. Azure 存储 Azure Key Vault 中的客户密钥包装帐户加密密钥。
5. 对于读/写操作，Azure 存储将请求发送到 Azure Key Vault，以便包装并解包帐户加密密钥，以执行加密和解密操作。

### <a name="enable-customer-managed-keys-for-a-storage-account"></a>为存储帐户启用客户管理的密钥

使用存储帐户的客户托管密钥启用加密时，Azure 存储会使用关联的密钥保管库中的客户密钥包装帐户加密密钥。 启用客户管理的密钥不会影响性能，并且会立即用新密钥加密帐户，而不会有任何时间延迟。

新的存储帐户始终使用 Microsoft 托管的密钥进行加密。 创建帐户时，不能启用客户管理的密钥。 客户托管的密钥存储在 Azure Key Vault 中，并且必须使用访问策略对密钥保管库进行预配，这些策略将对与存储帐户关联的托管标识授予密钥权限。 托管标识仅在创建存储帐户后可用。

若要了解如何将客户托管的密钥用于 Azure 存储加密的 Azure Key Vault，请参阅以下文章之一：

- [将客户托管的密钥配置为包含 Azure 门户中的 Azure 存储加密 Key Vault](storage-encryption-keys-portal.md)
- [通过 PowerShell 为 Azure 存储加密配置 Key Vault 的客户托管密钥](storage-encryption-keys-powershell.md)
- [将客户托管的密钥配置 Key Vault 用于从 Azure CLI 进行 Azure 存储加密](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> 客户托管的密钥依赖于 Azure 资源的托管标识，一项功能 Azure Active Directory （Azure AD）。 在 Azure 门户中配置客户管理的密钥时，会自动将托管标识分配给你的存储帐户。 如果随后将订阅、资源组或存储帐户从一个 Azure AD 目录移动到另一个目录，则不会将与存储帐户关联的托管标识传输到新租户，因此客户托管的密钥可能不再工作。 有关详细信息，请参阅在常见问题中**传输 Azure AD 目录之间的订阅**[和 Azure 资源的托管标识的已知问题](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>将客户托管的密钥存储在 Azure Key Vault

若要在存储帐户上启用客户管理的密钥，必须使用 Azure Key Vault 来存储密钥。 必须同时启用**软删除**和不**清除**密钥保管库中的属性。

密钥保管库必须位于与存储帐户相同的订阅中。 Azure 存储使用 Azure 资源的托管标识向密钥保管库进行身份验证，以便进行加密和解密操作。 托管标识当前不支持跨目录方案。

### <a name="rotate-customer-managed-keys"></a>轮换客户管理的密钥

你可以根据你的符合性策略，在 Azure Key Vault 中轮替客户管理的密钥。 当密钥旋转时，必须更新存储帐户以使用新的密钥 URI。 若要了解如何将存储帐户更新为在 Azure 门户中使用新版本的密钥，请参阅[使用 Azure 门户为 Azure 存储配置客户托管密钥](storage-encryption-keys-portal.md)中的 "**更新密钥版本**" 一节。

轮换密钥不会触发存储帐户中数据的重新加密。 用户无需执行任何其他操作。

### <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

若要撤消对客户管理的密钥的访问权限，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅[Azure Key Vault PowerShell](/powershell/module/az.keyvault//)或[Azure Key Vault CLI](/cli/azure/keyvault)。 有效地吊销访问权限会阻止对存储帐户中所有数据的访问，因为 Azure 存储无法访问加密密钥。

### <a name="customer-managed-keys-for-azure-managed-disks-preview"></a>Azure 托管磁盘的客户托管密钥（预览版）

客户管理的密钥也可用于管理 Azure 托管磁盘（预览版）的加密。 对于托管磁盘，客户管理的密钥的行为不同于 Azure 存储资源。 有关详细信息，请参阅适用于 Windows 的[azure 托管磁盘的服务器端加密](../../virtual-machines/windows/disk-encryption.md)或适用于 Linux[的 azure 托管磁盘的服务器端加密](../../virtual-machines/linux/disk-encryption.md)。

## <a name="customer-provided-keys-preview"></a>客户提供的密钥（预览）

对 Azure Blob 存储发出请求的客户端可以选择为单个请求提供加密密钥。 在请求中包含加密密钥可对 Blob 存储操作的加密设置进行精细控制。 客户提供的密钥（预览版）可存储在 Azure Key Vault 或其他密钥存储中。

有关演示如何为对 Blob 存储的请求指定客户提供的密钥的示例，请参阅[使用 .net 为 blob 存储的请求指定客户提供的密钥](../blobs/storage-blob-customer-provided-key.md)。 

### <a name="encrypting-read-and-write-operations"></a>加密读写操作

当客户端应用程序在请求中提供加密密钥时，Azure 存储会在读取和写入 blob 数据时以透明方式执行加密和解密。 Azure 存储会将加密密钥的 SHA-256 哈希与 blob 的内容一起写入。 哈希用于验证对 blob 的所有后续操作是否都使用相同的加密密钥。 

当客户端与请求一起发送时，Azure 存储不存储或管理加密密钥。 加密或解密过程完成后，密钥将被安全地丢弃。

当客户端使用客户提供的密钥创建或更新 blob 时，该 blob 的后续读取和写入请求还必须提供密钥。 如果未在已使用客户提供的密钥加密的 blob 的请求中提供密钥，则请求将失败，错误代码为409（冲突）。

如果客户端应用程序在请求上发送加密密钥，并且使用 Microsoft 托管密钥或客户管理的密钥加密存储帐户，则 Azure 存储使用请求中提供的密钥来加密和解密。

若要在请求中发送加密密钥，客户端必须使用 HTTPS 与 Azure 存储建立安全连接。

每个 blob 快照都可以有自己的加密密钥。

### <a name="request-headers-for-specifying-customer-provided-keys"></a>用于指定客户提供的密钥的请求标头

对于 REST 调用，客户端可以使用以下标头安全地将请求中的加密密钥信息传递到 Blob 存储：

|请求标头 | 描述 |
|---------------|-------------|
|`x-ms-encryption-key` |写入和读取请求均需要。 Base64 编码的256加密密钥值。 |
|`x-ms-encryption-key-sha256`| 写入和读取请求均需要。 Base64 编码的加密密钥 SHA256。 |
|`x-ms-encryption-algorithm` | 对于写入请求是必需的，对于读取请求是可选的。 指定使用给定的密钥加密数据时要使用的算法。 必须为 AES256。 |

为请求指定加密密钥是可选的。 但是，如果为写入操作指定上面列出的标头之一，则必须指定所有这些标头。

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>支持客户提供的密钥的 Blob 存储操作

以下 Blob 存储操作支持在请求中发送客户提供的加密密钥：

- [放置 Blob](/rest/api/storageservices/put-blob)
- [放置块列表](/rest/api/storageservices/put-block-list)
- [放置块](/rest/api/storageservices/put-block)
- [将块置于 URL 中](/rest/api/storageservices/put-block-from-url)
- [放置页](/rest/api/storageservices/put-page)
- [从 URL 放置页面](/rest/api/storageservices/put-page-from-url)
- [追加块](/rest/api/storageservices/append-block)
- [设置 Blob 属性](/rest/api/storageservices/set-blob-properties)
- [设置 Blob 元数据](/rest/api/storageservices/set-blob-metadata)
- [获取 Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)（获取 Blob 属性）
- [获取 Blob 元数据](/rest/api/storageservices/get-blob-metadata)
- [拍摄 Blob 快照](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>旋转客户提供的密钥

若要轮换请求上传递的加密密钥，请下载该 blob，并使用新的加密密钥重新上传。

> [!IMPORTANT]
> Azure 门户不能用于读取或写入使用请求中提供的密钥加密的容器或 blob。
>
> 请确保在 Azure Key Vault 的安全密钥存储中，对 Blob 存储的请求保护提供的加密密钥。 如果在没有加密密钥的情况下尝试对容器或 blob 进行写入操作，则操作将失败，并且你将失去对对象的访问权限。

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure 存储加密与磁盘加密

Azure 存储加密对备份 Azure 虚拟机磁盘的页 blob 进行了加密。 此外，还可以选择通过[Azure 磁盘加密](../../security/azure-security-disk-encryption-overview.md)对所有 Azure 虚拟机磁盘（包括本地临时磁盘）进行加密。 Azure 磁盘加密使用 Windows 上的行业标准[BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)和 Linux 上[的 dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt)来提供与 Azure Key Vault 集成的基于操作系统的加密解决方案。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 密钥保管库？](../../key-vault/key-vault-overview.md)
- [通过 Azure 门户配置客户托管密钥用于 Azure 存储加密](storage-encryption-keys-portal.md)
- [通过 PowerShell 配置客户托管密钥用于 Azure 存储加密](storage-encryption-keys-powershell.md)
- [通过 Azure CLI 配置客户托管密钥用于 Azure 存储加密](storage-encryption-keys-cli.md)

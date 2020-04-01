---
title: 使用 Azure 密钥保管库的客户管理密钥来管理帐户加密
titleSuffix: Azure Storage
description: 您可以使用自己的加密密钥来保护存储帐户中的数据。 指定客户管理的密钥时，该密钥用于保护和控制对加密数据的密钥的访问。 客户管理的密钥为管理访问控制提供了更大的灵活性。
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 6b5712094b9821dfa041cd5ba8617e86f7231bde
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478020"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>使用具有 Azure 密钥保管库的客户管理密钥来管理 Azure 存储加密

您可以使用自己的加密密钥来保护存储帐户中的数据。 指定客户管理的密钥时，该密钥用于保护和控制对加密数据的密钥的访问。 客户管理的密钥为管理访问控制提供了更大的灵活性。

必须使用 Azure Key Vault 来存储客户管理的密钥。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 存储帐户和 Key Vault 必须在同一个区域和同一个 Azure Active Directory (Azure AD) 租户中，但可以在不同的订阅中。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../../key-vault/key-vault-overview.md)

## <a name="about-customer-managed-keys"></a>关于客户管理的密钥

下图显示了 Azure 存储如何使用 Azure 活动目录和 Azure 密钥保管库使用客户管理的密钥发出请求：

![Azure 存储中客户管理的密钥的工作原理示意图](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

以下列表解释了示意图中带编号的步骤：

1. Azure Key Vault 管理员向与存储帐户关联的托管标识授予对加密密钥的权限。
2. Azure 存储管理员使用存储帐户的客户管理密钥配置加密。
3. Azure 存储使用与存储帐户关联的托管标识，对通过 Azure Active Directory 访问 Azure Key Vault 的活动进行身份验证。
4. Azure 存储使用 Azure Key Vault 中的客户密钥包装帐户加密密钥。
5. 对于读/写操作，Azure 存储向 Azure 密钥保管库发送请求以解包帐户加密密钥以执行加密和解密操作。

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>创建支持队列和表的客户管理密钥的帐户

在为存储帐户启用客户管理的密钥时，存储在队列和表服务中的数据不会由客户管理的密钥自动保护。 您可以选择在创建要包含在此保护中的存储帐户时配置这些服务。

有关如何创建支持队列和表的客户管理的密钥的存储帐户的详细信息，请参阅[创建一个帐户，该帐户支持表和队列的客户管理密钥](account-encryption-key-create.md)。

在为存储帐户配置客户管理的密钥时，Blob 和 File 服务中的数据始终受客户管理的密钥保护。

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>为存储帐户启用客户管理的密钥

客户管理的密钥只能在现有存储帐户上启用。 密钥保管库必须预配访问策略，这些策略向与存储帐户关联的托管标识授予密钥权限。 托管标识仅在存储帐户创建后可用。

配置客户管理的密钥时，Azure 存储使用关联的密钥保管库中的客户托管密钥包装帐户的根数据加密密钥。 启用客户管理的密钥不会影响性能，并立即生效。

如果通过启用或禁用客户托管密钥、更新密钥版本或指定其他密钥来修改用于 Azure 存储加密的密钥，则根密钥的加密会更改，但 Azure 存储帐户中的数据不需重新加密。

启用或禁用客户托管密钥或修改密钥或密钥版本时，根加密密钥的保护将更改，但 Azure 存储帐户中的数据不需要重新加密。

要了解如何将客户管理的密钥与 Azure 密钥保管库配合使用来对 Azure 存储进行加密，请参阅以下文章之一：

- [通过 Azure 门户使用密钥保管库配置客户管理的密钥用于 Azure 存储加密](storage-encryption-keys-portal.md)
- [通过 PowerShell 使用密钥保管库配置客户管理的密钥用于 Azure 存储加密](storage-encryption-keys-powershell.md)
- [通过 Azure CLI 使用密钥保管库配置客户管理的密钥用于 Azure 存储加密](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> 客户托管密钥依赖于 Azure 资源的托管标识，后者是Azure AD 的一项功能。 托管标识当前不支持跨目录方案。 在 Azure 门户中配置客户管理的密钥时，系统会在幕后自动将一个托管标识分配到你的存储帐户。 如果随后将订阅、资源组或存储帐户从一个 Azure AD 目录移到另一个目录，与存储帐户关联的托管标识不会传输到新租户，因此客户管理的密钥可能不再起作用。 有关详细信息，请参阅 [Azure 资源的常见问题解答和已知问题](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)中的“在 Azure AD 目录之间转移订阅”****。  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>将客户管理的密钥存储在 Azure 密钥保管库

若要在存储帐户上启用客户管理的密钥，必须使用 Azure 密钥保管库来存储密钥。 必须同时启用密钥保管库上的“软删除”和“不清除”属性********。

Azure 存储加密仅支持 2048 位 RSA 和 RSA-HSM 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)中的“Key Vault 密钥”。****

## <a name="rotate-customer-managed-keys"></a>轮换客户管理的密钥

可以根据自己的合规性策略，在 Azure 密钥保管库中轮换客户管理的密钥。 旋转密钥时，必须更新存储帐户才能使用新的密钥版本 URI。 若要了解如何更新存储帐户以在 Azure 门户中使用新版本的密钥，请参阅[使用 Azure 门户配置 Azure 存储的客户管理的密钥](storage-encryption-keys-portal.md)中标题为“更新密钥版本”的部分****。

轮换密钥不会触发存储帐户中数据的重新加密。 用户无需执行任何其他操作。

## <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

您可以随时撤销存储帐户对客户管理密钥的访问权限。 撤销对客户管理密钥的访问后，或在禁用或删除密钥后，客户端无法调用从 Blob 或其元数据读取或写入的操作。 尝试调用以下任一操作将失败，对于所有用户来说，错误代码 403（禁止）：

- [列表 Blob](/rest/api/storageservices/list-blobs)时，`include=metadata`使用请求 URI 上的参数调用
- [获取 Blob](/rest/api/storageservices/get-blob)
- [获取 Blob 属性](/rest/api/storageservices/get-blob-properties)
- [获取 Blob 元数据](/rest/api/storageservices/get-blob-metadata)
- [设置 Blob 元数据](/rest/api/storageservices/set-blob-metadata)
- [快照 Blob](/rest/api/storageservices/snapshot-blob)，当使用`x-ms-meta-name`请求标头调用时
- [复制 Blob](/rest/api/storageservices/copy-blob)
- [从 URL 复制 Blob](/rest/api/storageservices/copy-blob-from-url)
- [设置 Blob 层](/rest/api/storageservices/set-blob-tier)
- [放置块](/rest/api/storageservices/put-block)
- [从 URL 放置块](/rest/api/storageservices/put-block-from-url)
- [追加块](/rest/api/storageservices/append-block)
- [从 URL 追加块](/rest/api/storageservices/append-block-from-url)
- [放置 Blob](/rest/api/storageservices/put-blob)
- [放置页](/rest/api/storageservices/put-page)
- [从 URL 放置页面](/rest/api/storageservices/put-page-from-url)
- [增量复制 Blob](/rest/api/storageservices/incremental-copy-blob)

要再次调用这些操作，请恢复对客户管理的密钥的访问。

在撤销客户管理的密钥或禁用或删除密钥后，本节中未列出的所有数据操作都可能继续进行。

要撤消对客户管理密钥的访问，请使用[PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys)或[Azure CLI](storage-encryption-keys-cli.md#revoke-customer-managed-keys)。

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Azure 托管磁盘的客户托管密钥

客户管理的密钥也可用于管理 Azure 托管磁盘的加密。 客户管理的密钥对托管磁盘的行为不同于对 Azure 存储资源的行为。 有关详细信息，请参阅适用于 Linux 的 Windows 托管磁盘的 Azure[托管磁盘](../../virtual-machines/windows/disk-encryption.md)[的服务器端加密或服务器端加密](../../virtual-machines/linux/disk-encryption.md)。

## <a name="next-steps"></a>后续步骤

- [通过 Azure 门户使用密钥保管库配置客户管理的密钥用于 Azure 存储加密](storage-encryption-keys-portal.md)
- [通过 PowerShell 使用密钥保管库配置客户管理的密钥用于 Azure 存储加密](storage-encryption-keys-powershell.md)
- [通过 Azure CLI 使用密钥保管库配置客户管理的密钥用于 Azure 存储加密](storage-encryption-keys-cli.md)
- [静态数据的 Azure 存储加密](storage-service-encryption.md)

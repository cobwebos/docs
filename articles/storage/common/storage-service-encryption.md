---
title: 静态数据的 Azure 存储加密
description: Azure 存储在将数据保存到云之前会自动对其进行加密，以此保护数据。 可以依赖于使用 Microsoft 托管的密钥来加密存储帐户中的数据，也可以使用你自己的密钥来管理加密。
services: storage
author: tamram
ms.service: storage
ms.date: 08/24/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 1a5ed324ae109a151bf21050993bff02434410df
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814444"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>静态数据的 Azure 存储加密

将数据保存到云时，Azure 存储会自动加密数据。 Azure 存储加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。

## <a name="about-azure-storage-encryption"></a>关于 Azure 存储加密

Azure 存储中的数据将使用 256 位 [AES 加密法](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最强大块加密法之一）以透明方式进行加密和解密，并符合 FIPS 140-2 规范。 Azure 存储加密法类似于 Windows 上的 BitLocker 加密法。

已为所有存储帐户（包括资源管理器和经典存储帐户）启用 Azure 存储加密。 无法禁用 Azure 存储加密。 由于数据默认受到保护，因此无需修改代码或应用程序，即可利用 Azure 存储加密。

不管存储帐户的性能层级（标准或高级）、访问层级（热访问层或冷访问层）或部署模型（Azure 资源管理器或经典）如何，都会将存储帐户中的数据加密。 存档层级中的所有 blob 也都是加密的。 所有 Azure 存储冗余选项都支持加密，当启用了异地复制时，会对主要区域和次要区域中的所有数据进行加密。 所有 Azure 存储资源（包括 Blob、磁盘、文件、队列和表）都会加密。 所有对象元数据也会加密。 Azure 存储加密不会产生额外的费用。

2017 年 10 月 20 日后写入 Azure 存储的每个块 Blob、追加 Blob 或页 Blob 均已加密。 在此日期之前创建的 Blob 继续由后台进程加密。 若要强制对 2017 年 10 月 20 日之前创建的 Blob 进行加密，可以重写 Blob。 若要了解如何检查 Blob 的加密状态，请参阅 [检查 Blob 的加密状态](../blobs/storage-blob-encryption-status.md)。

有关 Azure 存储加密的底层加密模块的详细信息，请参见[加密 API：下一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)。

有关 Azure 托管磁盘的加密和密钥管理的信息，请参阅适用于 Windows VM 的 [Azure 托管磁盘的服务器端加密](../../virtual-machines/windows/disk-encryption.md)或适用于 Linux VM 的 [Azure 托管磁盘的服务器端加密](../../virtual-machines/linux/disk-encryption.md)。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，新存储帐户中的数据使用 Microsoft 托管的密钥进行加密。 你可以继续依赖 Microsoft 托管的密钥来加密数据，也可以通过自己的密钥来管理加密。 如果选择使用自己的密钥管理加密，则有两个选择。 你可以使用两种类型的密钥管理，或同时使用这两种类型：

- 你可以为 Azure Key Vault 指定“客户管理的密钥”，以用于加密和解密 Blob 存储和 Azure 文件中的数据。<sup>1,2</sup> 有关客户管理的密钥的详细信息，请参阅[将客户管理的密钥与 Azure Key Vault 配合使用来管理 Azure 存储加密](encryption-customer-managed-keys.md)。
- 可以在 Blob 存储操作中指定客户提供的密钥。 对 Blob 存储发出读取或写入请求的客户端可以在请求中包含加密密钥，以便精细控制 Blob 数据的加密和解密方式。 有关客户提供的密钥的详细信息，请参阅 [在对 Blob 存储的请求中提供加密密钥](encryption-customer-provided-keys.md)。

下表比较了 Azure 存储加密的密钥管理选项。

| 密钥管理参数 | Microsoft 管理的密钥 | 客户管理的密钥 | 客户提供的密钥 |
|--|--|--|--|
| 加密/解密操作 | Azure | Azure | Azure |
| 支持的 Azure 存储服务 | 全部 | Blob 存储，Azure 文件<sup>1，2</sup> | Blob 存储 |
| 密钥存储 | Microsoft 密钥存储 | Azure Key Vault | 客户自己的密钥存储 |
| 密钥轮换责任 | Microsoft | 客户 | 客户 |
| 密钥控制 | Microsoft | 客户 | 客户 |

<sup>1</sup> 有关创建支持对队列存储使用客户托管密钥的帐户的信息，请参阅 [创建支持队列的客户托管密钥的帐户](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。<br />
<sup>2</sup> 有关创建支持使用客户管理的密钥和表存储的帐户的信息，请参阅 [创建支持表的客户托管密钥的帐户](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)。

> [!NOTE]
> Microsoft 托管的密钥根据符合性要求进行适当的轮替。 如果有特定的密钥轮换要求，Microsoft 建议移动到客户管理的密钥，以便您可以自行管理和审核旋转。

## <a name="encryption-scopes-for-blob-storage-preview"></a>Blob 存储 (预览的加密范围) 

默认情况下，存储帐户使用作用域为存储帐户的密钥进行加密。 你可以选择使用 Microsoft 管理的密钥或存储在 Azure Key Vault 中的客户托管密钥来保护和控制对加密数据的密钥的访问权限。

使用加密范围，可以选择在容器级别或单个 blob 上管理加密。 你可以使用加密作用域在驻留在同一存储帐户中但属于不同客户的数据之间创建安全边界。

可以使用 Azure 存储资源提供程序为存储帐户创建一个或多个加密作用域。 当你创建加密作用域时，你可以指定是使用 Microsoft 托管密钥还是使用存储在 Azure Key Vault 中的客户托管密钥来保护作用域。 同一存储帐户上的不同加密范围可以使用 Microsoft 管理的密钥或客户管理的密钥。

创建加密作用域后，可以在请求上指定加密作用域，以创建容器或 blob。 有关如何创建加密范围的详细信息，请参阅 [创建和管理加密范围 (预览) ](../blobs/encryption-scope-manage.md)。

> [!NOTE]
> 读取访问异地冗余存储不支持加密作用域 (GRS) ，并在预览期间 (GZRS) 帐户读取访问地域冗余存储。

> [!IMPORTANT]
> 加密范围预览版仅适用于非生产使用。 生产服务级别协议 (SLA) 当前不可用。
>
> 若要避免意外的成本，请务必禁用当前不需要的任何加密作用域。

### <a name="create-a-container-or-blob-with-an-encryption-scope"></a>使用加密范围创建容器或 blob

在加密范围下创建的 blob 使用为该作用域指定的密钥进行加密。 创建 blob 时，可以为单个 blob 指定加密作用域，也可以在创建容器时指定默认的加密作用域。 当在容器级别指定默认加密范围时，该容器中的所有 blob 都将使用与默认作用域相关联的密钥进行加密。

当你在具有默认加密作用域的容器中创建 blob 时，如果将容器配置为允许替代默认加密范围，则可以指定覆盖默认加密范围的加密作用域。 若要防止覆盖默认的加密范围，请将容器配置为拒绝单个 blob 的替代。

属于加密作用域的 blob 上的读取操作以透明方式发生，前提是未禁用加密作用域。

### <a name="disable-an-encryption-scope"></a>禁用加密范围

禁用加密作用域时，将会失败，并出现 HTTP 错误代码 403 (禁止) ，从而导致对加密作用域执行的任何后续读取或写入操作失败。 如果重新启用加密范围，读取和写入操作将再次正常继续。

禁用加密作用域后，你将不再向其收费。 禁用不需要的任何加密范围以避免不必要的费用。

如果你的加密范围受 Azure Key Vault 的客户托管密钥保护，则还可以删除密钥保管库中的关联密钥，以便禁用加密作用域。 请记住，Azure Key Vault 中的客户托管密钥受到软删除和清除保护的保护，并且已删除的密钥服从这些属性定义的行为。 有关详细信息，请参阅 Azure Key Vault 文档中的以下主题之一：

- [如何在 PowerShell 中使用软删除](../../key-vault/general/soft-delete-powershell.md)
- [如何在 CLI 中使用软删除](../../key-vault/general/soft-delete-cli.md)

> [!NOTE]
> 不能删除加密作用域。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 密钥保管库？](../../key-vault/general/overview.md)
- [通过 Azure 门户配置客户管理的密钥用于 Azure 存储加密](storage-encryption-keys-portal.md)
- [通过 PowerShell 配置客户管理的密钥用于 Azure 存储加密](storage-encryption-keys-powershell.md)
- [通过 Azure CLI 配置客户托管密钥用于 Azure 存储加密](storage-encryption-keys-cli.md)

---
title: 静态数据的 Azure 存储加密
description: Azure 存储在将数据保存到云之前会自动对其进行加密，以此保护数据。 可以依赖于使用 Microsoft 托管的密钥来加密存储帐户中的数据，也可以使用你自己的密钥来管理加密。
services: storage
author: tamram
ms.service: storage
ms.date: 06/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 8b4236e40e8dfbe6ce67bca007be0b6737a6e0c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84945573"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>静态数据的 Azure 存储加密

Azure 存储在将数据保存到云时会自动加密数据。 Azure 存储加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。

## <a name="about-azure-storage-encryption"></a>关于 Azure 存储加密

Azure 存储中的数据将使用 256 位 [AES 加密法](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最强大块加密法之一）以透明方式进行加密和解密，并符合 FIPS 140-2 规范。 Azure 存储加密法类似于 Windows 上的 BitLocker 加密法。

已为所有存储帐户（包括资源管理器和经典存储帐户）启用 Azure 存储加密。 无法禁用 Azure 存储加密。 由于数据默认受到保护，因此无需修改代码或应用程序，即可利用 Azure 存储加密。

不管存储帐户的性能层级（标准或高级）、访问层级（热访问层或冷访问层）或部署模型（Azure 资源管理器或经典）如何，都会将存储帐户中的数据加密。 存档层级中的所有 blob 也都是加密的。 所有 Azure 存储冗余选项都支持加密，当启用了异地复制时，会对主要区域和次要区域中的所有数据进行加密。 所有 Azure 存储资源（包括 Blob、磁盘、文件、队列和表）都会加密。 所有对象元数据也会加密。 Azure 存储加密不会产生额外的费用。

2017 年 10 月 20 日后写入 Azure 存储的每个块 Blob、追加 Blob 或页 Blob 均已加密。 在此日期之前创建的 Blob 继续由后台进程加密。 若要强制对 2017 年 10 月 20 日之前创建的 Blob 进行加密，可以重写 Blob。 若要了解如何检查 Blob 的加密状态，请参阅 [检查 Blob 的加密状态](../blobs/storage-blob-encryption-status.md)。

有关 Azure 存储加密的底层加密模块的详细信息，请参见[加密 API：下一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

新存储帐户中的数据使用 Microsoft 托管密钥加密。 可以依赖于使用 Microsoft 托管的密钥来加密数据，也可以使用你自己的密钥来管理加密。 如果你选择使用自己的密钥来管理加密，则可以采用两种做法：

- 你可以为 Azure Key Vault 指定“客户管理的密钥”，以用于加密和解密 Blob 存储和 Azure 文件中的数据。<sup>1,2</sup> 有关客户管理的密钥的详细信息，请参阅[将客户管理的密钥与 Azure Key Vault 配合使用来管理 Azure 存储加密](encryption-customer-managed-keys.md)。
- 可以在 Blob 存储操作中指定客户提供的密钥。 对 Blob 存储发出读取或写入请求的客户端可以在请求中包含加密密钥，以便精细控制 Blob 数据的加密和解密方式。 有关客户提供的密钥的详细信息，请参阅[在对 Blob 存储的请求中提供加密密钥（预览版）](encryption-customer-provided-keys.md)。

下表比较了 Azure 存储加密的密钥管理选项。

|                                        |    Microsoft 管理的密钥                             |    客户管理的密钥                                                                                                                        |    客户提供的密钥                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    加密/解密操作    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    支持的 Azure 存储服务    |    全部                                                |    Blob 存储，Azure 文件<sup>1，2</sup>                                                                                                               |    Blob 存储                                                                  |
|    密钥存储                         |    Microsoft 密钥存储    |    Azure Key Vault                                                                                                                              |    客户自己的密钥存储                                                                 |
|    密钥轮换责任         |    Microsoft                                          |    客户                                                                                                                                     |    客户                                                                      |
|    密钥控制                          |    Microsoft                                     |    客户                                                                                                                    |    客户                                                                 |

<sup>1</sup>有关创建支持对队列存储使用客户托管密钥的帐户的信息，请参阅[创建支持队列的客户托管密钥的帐户](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。<br />
<sup>2</sup>有关创建支持使用客户管理的密钥和表存储的帐户的信息，请参阅[创建支持表的客户托管密钥的帐户](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)。

有关 Azure 托管磁盘的加密和密钥管理的信息，请参阅适用于 Windows Vm[的 azure 托管磁盘的服务器端加密](../../virtual-machines/windows/disk-encryption.md)或适用于 Linux Vm 的[azure 托管磁盘的服务器端加密](../../virtual-machines/linux/disk-encryption.md)。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 密钥保管库？](../../key-vault/general/overview.md)
- [通过 Azure 门户配置客户管理的密钥用于 Azure 存储加密](storage-encryption-keys-portal.md)
- [通过 PowerShell 配置客户管理的密钥用于 Azure 存储加密](storage-encryption-keys-powershell.md)
- [通过 Azure CLI 配置客户托管密钥用于 Azure 存储加密](storage-encryption-keys-cli.md)

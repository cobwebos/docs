---
title: 静态数据的 Azure 存储服务加密 | Microsoft Docs
description: 使用 Azure 存储服务加密功能可在存储数据时在服务端加密 Azure Blob 存储，并在检索数据时解密数据。
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 08/01/2018
ms.author: lakasa
ms.openlocfilehash: f35697139a4be49be8a645cfd4d451ad8e3c8094
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412349"
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>静态数据的 Azure 存储服务加密
静态数据的 Azure 存储服务加密可帮助保护数据，使组织能够信守在安全性与合规性方面所做的承诺。 使用此功能，Azure 存储平台可以先自动加密数据，然后将其保存到 Azure Blob 存储、Azure 文件或 Azure 队列存储，并在检索之前解密数据。 存储服务加密中的加密、静态加密、解密和密钥管理的处理对用户是透明的。 通过 256 位 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)加密所有写入 Azure 存储平台的数据，AES 加密是现在最强大的分组加密之一。

针对所有新的和现有的存储帐户启用存储服务加密，并且不能禁用。 因为数据默认受保护，所以无需修改代码或应用程序，即可使用存储服务加密。

该功能会自动加密以下位置的数据：

- Azure Blob 存储、Azure 文件、Azure 队列存储、Azure 表存储。  
- 两个性能层（标准和高级）。
- 两个部署模型（Azure 资源管理器模型和经典模型）。

> [!Note]  
> 存储服务加密不适用于 [Azure 托管磁盘](../../virtual-machines/windows/managed-disks-overview.md)。 我们建议使用 OS 级别的加密（例如 [Azure 磁盘加密](../../security/azure-security-disk-encryption-overview.md)），此类加密在 Windows 上使用行业标准的 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)，在 Linux 上使用 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)，以提供与 KeyVault 集成的加密。

存储服务加密不影响 Azure 存储服务的性能。

可以将 Microsoft 托管的加密密钥用于存储服务加密，或者使用你自己的加密密钥。 有关使用自己密钥的详细信息，请参阅[在 Azure Key Vault 中使用客户托管密钥进行存储服务加密](storage-service-encryption-customer-managed-keys.md)。

## <a name="view-encryption-settings-in-the-azure-portal"></a>在 Azure 门户中查看加密设置
要查看存储服务加密设置，请登录 [Azure 门户](https://portal.azure.com)，并选择存储帐户。 在“设置”窗格中，选择“加密”设置。

![显示加密设置的门户截图](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>存储服务加密的常见问题解答
**如何在资源管理器存储帐户中加密数据？**  
会为所有存储帐户（经典和资源管理器）启用存储服务加密，启用加密前创建的存储帐户中的任何已有文件都会由后台加密进程以追溯方式进行加密。

**创建存储帐户时，是否会默认启用存储服务加密？**  
是的，会为所有存储帐户和所有 Azure 存储服务启用存储服务加密。

**我有一个资源管理器存储帐户。能否对其启用存储服务加密？**  
默认对所有现有的资源管理器存储帐户启用存储服务加密。 Azure Blob 存储、Azure 文件、Azure 队列存储和表存储均支持此类加密。 

**是否可以在存储帐户中禁用加密？**  
加密默认已启用，无法预配为禁用存储帐户加密。 

**如果启用存储服务加密，需另付多少 Azure 存储费用？**  
没有任何额外费用。

**我可以使用自己的加密密钥吗？**  
是的，可以使用自己的加密密钥。 有关详细信息，请参阅 [Azure Key Vault 中使用客户托管密钥的存储服务加密](storage-service-encryption-customer-managed-keys.md)。

**是否可以吊销加密密钥的访问权限？**  
是的，如果在 Azure Key Vault 中[使用自己的加密密钥](storage-service-encryption-customer-managed-keys.md)。

**存储服务加密是否适用于 Azure 托管磁盘？**  
否。存储服务加密不适用于 [Azure 托管磁盘](../../virtual-machines/windows/managed-disks-overview.md)。 我们建议使用 OS 级别的加密（例如 [Azure 磁盘加密](../../security/azure-security-disk-encryption-overview.md)），此类加密在 Windows 上使用行业标准的 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)，在 Linux 上使用 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)，以提供与 KeyVault 集成的加密。

**存储服务加密与 Azure 磁盘加密有何不同？**  
Azure 磁盘加密在基于 OS 的解决方案（例如 BitLocker、DM-Crypt 和 Azure KeyVault）之间提供集成。 存储服务加密在虚拟机下面的 Azure 存储平台层上提供本机加密。

**我有一个经典存储帐户。能否对其启用存储服务加密？**  
会对所有存储帐户（经典和资源管理器）启用存储服务加密。

**如何在经典存储帐户中加密数据？**  
默认启用加密后，Azure 存储服务中存储的所有数据会自动加密。 

**是否可以使用 Azure PowerShell 和 Azure CLI 创建存储帐户并启用存储服务加密？**  
在创建任何存储帐户（经典或资源管理器）时默认启用存储服务加密。 可以使用 Azure PowerShell 和 Azure CLI 验证帐户属性。

**我的存储帐户设置为异地冗余复制。如果启用存储服务加密，冗余副本是否也会加密？**  
是的，所有存储帐户的副本都会加密。 支持所有冗余选项 - 本地冗余存储、区域冗余存储、异地冗余存储和读取访问异地冗余存储。

**存储服务加密是否仅允许在特定区域使用？**  
存储服务加密已在所有区域推出。

**存储服务加密是否符合 FIPS 140-2？**  
是的，存储服务加密符合 FIPS 140-2。

**遇到问题或想要提供反馈时如何联系你们？**  
如有任何有关存储服务加密的问题或反馈，请联系 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)。

## <a name="next-steps"></a>后续步骤
Azure 存储提供一整套安全性功能，这些功能相辅相成，帮助开发人员构建安全的应用程序。 有关详细信息，请参阅[存储安全指南](../storage-security-guide.md)。
---
title: 静态数据的 Azure 存储服务加密 | Microsoft Docs
description: 使用 Azure 存储服务加密功能可在存储数据时在服务端加密 Azure Blob 存储，并在检索数据时解密数据。
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: lakasa
ms.openlocfilehash: 6b56cbb4220ce1c8767724938dd531b8ae5c3920
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>静态数据的 Azure 存储服务加密

静态数据的 Azure 存储服务加密可帮助保护数据，使组织能够信守在安全性与合规性方面所做的承诺。 使用此功能，Azure 存储可以先自动加密数据，再将其保存到 Azure 存储，并在检索之前解密数据。 存储服务加密中的加密、静态加密、解密和密钥管理的处理对用户是透明的。 通过 256 位 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)加密所有写入 Azure 存储的数据，AES 加密是现在最强大的分组加密之一。

针对所有新的和现有的存储帐户启用存储服务加密，并且不能禁用。 因为数据默认受保护，所以无需修改代码或应用程序，即可使用存储服务加密。

该功能会自动加密以下位置的数据：

- 两个性能层（标准和高级）。
- 两个部署模型（Azure 资源管理器模型和经典模型）。
- 所有 Azure 存储服务（Blob 存储、队列存储、表存储和 Azure 文件）。 

存储服务加密不影响 Azure 存储性能。

可以将 Microsoft 托管的加密密钥用于存储服务加密，或者使用你自己的加密密钥。 有关使用自己密钥的详细信息，请参阅[在 Azure Key Vault 中使用客户托管密钥进行存储服务加密](storage-service-encryption-customer-managed-keys.md)。

## <a name="view-encryption-settings-in-the-azure-portal"></a>在 Azure 门户中查看加密设置

要查看存储服务加密设置，请登录 [Azure 门户](https://portal.azure.com)，并选择存储帐户。 在“设置”窗格中，选择“加密”设置。

![显示加密设置的门户截图](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>存储服务加密的常见问题解答

**问：若已有经典存储帐户。能否对其启用存储服务加密？**

答：默认对所有存储帐户（经典和资源管理器）启用存储服务加密。

**问：如何在经典存储帐户中加密数据？**

答：默认启用加密，Azure 存储自动加密新数据。 

**答：若已有资源管理器存储帐户。能否对其启用存储服务加密？**

答：默认对所有现有的资源管理器存储帐户启用存储服务加密。 Blob 存储、表存储、队列存储和 Azure 文件均支持。 

**问：如何在资源管理器存储帐户中加密数据？**

答：默认已对所有存储帐户（经典存储帐户和资源管理存储帐户）启用存储服务加密。 但是，现有数据不会加密。 要加密现有数据，可将它们复制为另一个名称或复制到另一个容器，并删除未加密的版本。 

**问：是否可以使用 Azure PowerShell 和 Azure CLI 创建存储帐户并启用存储服务加密？**

答：在创建任何存储帐户（经典或资源管理器）时默认启用存储服务帐户。 可以使用 Azure PowerShell 和 Azure CLI 验证帐户属性。

**问：如果启用存储服务加密，需另付多少 Azure 存储费用？**

答：没有任何额外费用。

**问：加密密钥由谁管理？**

答：Microsoft 管理密钥。

**问：我可以使用自己的加密密钥吗？**

答：目前没有。

**问：是否可以吊销加密密钥的访问权限？**

答：目前没有。 Microsoft 全权管理密钥。

**问：创建存储帐户时，是否会默认启用存储服务加密？**

答：默认已对所有存储帐户启用使用 Microsoft 托管密钥的存储服务帐户- Azure 资源管理器帐户和经典存储帐户。 对所有服务启用 - Blob 存储、表存储、队列存储和 Azure 文件。

**问：此功能与 Azure 磁盘加密有何不同？**

答：Azure 磁盘加密用于加密IaaS VM 中的 OS 和数据磁盘。 有关详细信息，请参阅[存储安全指南](../storage-security-guide.md)。

**问：如果在数据磁盘上启用了 Azure 磁盘加密会怎样？**

答：不会有任何问题。 这两种方法都将加密数据。

**问：我的存储帐户设置为异地冗余复制。如果启用存储服务加密，冗余副本是否也会加密？**

答：所有存储帐户的副本都会加密。 支持所有冗余选项 - 本地冗余存储、区域冗余存储、异地冗余存储和读取访问异地冗余存储。

**问：是否能禁用存储账户上的加密？**

答：加密默认已启用，无法预配为禁用存储帐户加密。 

**问：存储服务加密是否仅允许在特定区域使用？**

答：存储服务加密在所有区域针对所有服务提供。 

**问：如果遇到问题或想要提供反馈时怎样进行联系？**

答：任何有关存储服务加密的问题或反馈，请联系 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)。

## <a name="next-steps"></a>后续步骤
Azure 存储提供一整套安全性功能，这些功能相辅相成，帮助开发人员构建安全的应用程序。 有关详细信息，请参阅[存储安全指南](../storage-security-guide.md)。

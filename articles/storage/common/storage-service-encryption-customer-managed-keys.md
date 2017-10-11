---
title: "Azure Key Vault 中使用客户托管密钥的 Azure 存储服务加密 | Microsoft Docs"
description: "存储数据时，可以使用 Azure 存储服务加密功能在服务端加密 Azure Blob 存储；使用客户托管的密钥检查数据时，可以使用此功能来解密存储。"
services: storage
documentationcenter: .net
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: lakasa
ms.openlocfilehash: 6d1e6752fb631114f5be06cb27a63e40547bf6ca
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault 中使用客户托管密钥的存储服务加密

Microsoft Azure 致力于帮助保护数据，使组织能够信守在安全性与合规性方面所做的承诺。  保护静态数据的方法之一是使用存储服务加密 (SSE)，此功能可以在将数据写入存储时自动加密数据，在检索数据时解密数据。 加密和解密是自动且完全透明的，它使用 256 位 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)，这是行业中最强的分组加密法之一。

可以将 Microsoft 托管的加密密钥用于 SSE，或者使用你自己的加密密钥。 本文介绍后一种方法。 有关使用 Microsoft 托管密钥的详细信息，或有关 SSE 的一般信息，请参阅[静态数据的存储服务加密](../storage-service-encryption.md)。

为了让你使用自己的加密密钥，用于 Blob 存储的 SSE 已与 Azure Key Vault (AKV) 集成。 你可以创建自己的加密密钥并将其存储在 AKV 中，或使用 AKV 的 API 来生成加密密钥。 AKV 不仅可以让你管理和控制自己的密钥，而且还可让你审核密钥用法。 

为何要创建自己的密钥？ 这样可以提高灵活性，从而可以创建、轮换、禁用和定义访问控制。 此外，还可以审核用于保护数据的加密密钥。

## <a name="sse-with-customer-managed-keys-preview"></a>使用客户托管密钥的 SSE 预览版

此功能目前处于预览状态。 若要使用此功能，需要创建新的存储帐户。 可以创建新的 Key Vault 和密钥，或者使用现有的 Key Vault 和密钥。 存储帐户和 Key Vault 必须在同一个区域中，但可以在不同的订阅中。

若要参与预览，请联系 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)。我们将提供特殊的链接让你参与预览。

若要了解详细信息，请参阅[常见问题解答](#frequently-asked-questions-about-storage-service-encryption-for-data-at-rest)。

> [!IMPORTANT]
> 遵循本文中的步骤之前，必须注册预览版。 在无法访问预览版的情况下，将无法在门户中启用此功能。

## <a name="getting-started"></a>入门
## <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>步骤 1：[创建新存储帐户](../storage-create-storage-account.md)

## <a name="step-2-enable-encryption"></a>步骤 2：启用加密
可以使用 [Azure 门户](https://portal.azure.com)为存储帐户启用 SSE。 在存储帐户的“设置”边栏选项卡中，找到如下图所示的“Blob 服务”部分，并单击“加密”。

![显示加密选项的门户截图](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)
<br/>*为 Blob 服务启用 SSE*

如果想要以编程方式启用或禁用存储帐户上的存储服务加密，可以使用 [Azure 存储资源提供程序 REST API](https://docs.microsoft.com/en-us/rest/api/storagerp/?redirectedfrom=MSDN)、[.NET 存储资源提供程序](https://docs.microsoft.com/en-us/dotnet/api/?redirectedfrom=MSDN)、[Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-4.0.0) 或 [Azure CLI](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli)。

如果此屏幕上未显示“使用你自己的密钥”复选框，则表示你尚未通过预览版的批准。 请向 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) 发送电子邮件请求批准。

![显示加密预览版的门户屏幕截图](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

默认情况下，SSE 使用 Microsoft 托管的密钥。 若要使用你自己的密钥，请选中相应的框。 然后，可以指定密钥 URI，或者从选择器中选择密钥和 Key Vault。

## <a name="step-3-select-your-key"></a>步骤 3：选择密钥

![显示加密功能中使用自己密钥选项的门户屏幕截图](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

![显示加密功能中用于输入密钥 URI 的选项的门户屏幕截图](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

如果存储帐户无权访问 Key Vault，你可以使用 Azure Powershell 运行以下命令，向存储帐户授予对所需 Key Vault 的访问权限。

![显示拒绝访问 Key Vault 的门户屏幕截图](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

此外，可以通过 Azure 门户授予访问权限，方法是在 Azure 门户中转到“Azure Key Vault”，然后向存储帐户授予访问权限。

## <a name="step-4-copy-data-to-storage-account"></a>步骤 4：将数据复制到存储帐户
若要将数据传输到新存储帐户中以便对其进行加密，请参阅[“静态数据的存储服务加密”的“入门”中所述的“步骤 3”](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-3-copy-data-to-storage-account)。

## <a name="step-5-query-the-status-of-the-encrypted-data"></a>步骤 5：查询加密数据的状态
若要查询加密数据的状态，请参阅[“静态数据的存储服务加密”的“入门”中所述的“步骤 4”](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-4-query-the-status-of-the-encrypted-data)。

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>有关静态数据存储服务加密的常见问题
**问：我正在使用高级存储；是否可以将 SSE 与客户托管的密钥配合使用？**

答：可以，标准存储和高级存储都支持将 SSE 与 Microsoft 托管的密钥和客户托管的密钥配合使用。 

**问：是否可以在启用使用客户托管密钥的 SSE 的情况下，使用 Azure PowerShell 和 Azure CLI 创建新存储帐户？**

答：是的。

**问：如果已启用使用客户托管密钥的 SSE，Azure 存储的费用将会高多少？**

答：会产生 Azure Key Vault 相关的使用费。 有关详细信息，请访问 [Key Vault 定价](https://azure.microsoft.com/en-us/pricing/details/key-vault/)。 使用 SSE 不会产生额外的费用。

**问：是否可以吊销加密密钥的访问权限？**

答：是，随时可以吊销访问权限。 可通过多种方式吊销对密钥的访问权限。 有关详细信息，请参阅 [Azure Key Vault PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/?view=azurermps-4.0.0) 和 [Azure Key Vault CLI](https://docs.microsoft.com/en-us/cli/azure/keyvault)。 吊销访问权限会实际阻止对存储帐户中所有 Blob 的访问权限，因为 Azure 存储帐户无法访问帐户加密密钥。

**问：是否可以在不同的区域创建存储帐户和密钥？**

答：不可以，存储帐户和 Key Vault/密钥需在同一个区域。 

**问：创建存储帐户时是否可以启用使用客户托管密钥的 SSE？**

答：没有。 如果在创建存储帐户时启用 SSE，则只能使用 Microsoft 托管的密钥。 若要使用客户托管的密钥，必须更新存储帐户属性。 可以使用 REST 或某个存储客户端库以编程方式更新存储帐户，或者在创建帐户后使用 Azure 门户更新存储帐户属性。

**问：使用采用客户托管密钥的 SSE 时是否可以禁用加密？**

答：使用采用客户托管密钥的 SSE 时无法禁用加密。 若要禁用加密，必须改用 Microsoft 托管的密钥。 可以使用 Azure 门户或 PowerShell 执行此操作。

**问：创建新的存储帐户时是否会按默认启用 SSE？**

答：默认情况下不启用 SSE；可以使用 Azure 门户来启用它。 也可以编程方式使用存储资源提供程序 REST API 来启用此功能。 

**问：我无法对我的存储帐户启用加密。**

答：它是 Resource Manager 存储帐户吗？ 不支持经典存储帐户。 此外，只能在新建的 Resource Manager 存储帐户中启用使用客户托管密钥的 SSE。

**问：是否只有特定的区域允许使用客户托管密钥的 SSE？**

答：此 SSE 预览版只能在 Blob 存储的特定区域中使用。 请向 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) 发送电子邮件，咨询预览版的可用性与详细信息。 

**问：如果遇到问题或想要提供反馈时怎样进行联系？**

答：任何有关存储服务加密的问题，请联系 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)。 

## <a name="next-steps"></a>后续步骤

*   有关可帮助开发人员构建安全应用程序的综合安全功能集的详细信息，请参阅[存储安全指南](https://docs.microsoft.com/en-us/azure/storage/storage-security-guide)。
*   有关 Azure Key Vault的概述信息，请参阅[什么是 Azure Key Vault？](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis)
*   有关 Azure Key Vault 的入门信息，请参阅 [Azure Key Vault 入门](../../key-vault/key-vault-get-started.md)。

---
title: Azure Key Vault 中使用客户托管密钥的 Azure 存储服务加密 | Microsoft Docs
description: 存储数据时，可以使用 Azure 存储服务加密功能在服务端加密 Azure Blob 存储；使用客户托管的密钥检查数据时，可以使用此功能来解密存储。
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/07/2018
ms.author: lakasa
ms.openlocfilehash: b40858640d10e5661be420976520774bd50837cb
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault 中使用客户托管密钥的存储服务加密

Microsoft Azure 致力于帮助保护数据，使组织能够信守在安全性与合规性方面所做的承诺。 Azure 存储保护数据的方法之一是使用存储服务加密 (SSE)，此功能可以在将数据写入存储时加密数据，在检索数据时解密数据。 加密和解密是自动且透明的，它使用 256 位 [AES 加密](https://wikipedia.org/wiki/Advanced_Encryption_Standard)，这是行业中最强的分组加密法之一。

可以将 Microsoft 托管的加密密钥用于 SSE，或者使用你自己的加密密钥。 本文介绍如何使用自己的加密密钥。 有关使用 Microsoft 托管密钥的详细信息，或有关 SSE 的一般信息，请参阅[静态数据的存储服务加密](storage-service-encryption.md)。

Blob 和文件存储的 SSE 与 Azure Key Vault 集成，因此可以使用 Key Vault 来管理加密密钥。 你可以创建自己的加密密钥并将其存储在 Key Vault 中，或使用 Azure Key Vault 的 API 来生成加密密钥。 使用 Azure Key Vault 可以管理和控制密钥，还可以审核密钥用法。

为什么创建自己的密钥？ 自定义密钥可以提高灵活性，从而可以创建、轮换、禁用和定义访问控制。 自定义密钥还可以审核用于保护数据的加密密钥。

## <a name="get-started-with-customer-managed-keys"></a>客户托管密钥入门

若要将客户托管密钥用于 SSE，可以创建新的 Key Vault 和密钥，或者使用现有的 Key Vault 和密钥。 存储帐户和 Key Vault 必须在同一个区域中，但可以在不同的订阅中。 

### <a name="step-1-create-a-storage-account"></a>步骤 1：创建存储帐户

首先，如果还没有存储帐户，请创建存储帐户。 有关详细信息，请参阅[创建新的存储帐户](storage-quickstart-create-account.md)。

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>步骤 2：为 Blob 和文件存储启用 SSE

若要启用使用客户托管密钥的 SSE，还必须启用两个密钥保护功能：软删除和不清除。 这些设置可确保密钥不被意外或有意地删除。 密钥的最大保持期设置为 90 天，保护用户免受恶意执行组件或勒索软件攻击。

如果想要以编程方式启用使用客户托管密钥的 SSE，可以使用 [Azure 存储资源提供程序 REST API](https://docs.microsoft.com/rest/api/storagerp)、[.NET 存储资源提供程序客户端库](https://docs.microsoft.com/dotnet/api)、[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 或 [Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli)。

若要将客户托管密钥用于 SSE，必须向存储帐户分配存储帐户标识。 可以通过执行以下 PowerShell 命令设置标识：

```powershell
Set-AzureRmStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

可以通过执行以下 PowerShell 命令启用软删除和不清除：

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enableSoftDelete -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties

($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enablePurgeProtection -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties
```

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>步骤 3：使用客户托管密钥启用加密

默认情况下，SSE 使用 Microsoft 托管的密钥。 可以使用 [Azure 门户](https://portal.azure.com/)为存储帐户启用使用客户托管密钥的 SSE。 在存储帐户的“设置”边栏选项卡上，单击“加密”。 选择“使用自己的密钥”选项，如下图所示。

![显示加密选项的门户截图](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>步骤 4：选择密钥

可以将密钥指定为 URI，或从 Key Vault 中选择密钥。

#### <a name="specify-a-key-as-a-uri"></a>将密钥指定为 URI

若要从 URI 指定密钥，请执行以下步骤：

1. 选择“输入密钥 URI”选项。  
2. 在“密钥 URI”字段中，指定 URI。

    ![显示加密功能中用于输入密钥 URI 的选项的门户屏幕截图](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

#### <a name="specify-a-key-from-a-key-vault"></a>从 Key Vault 指定密钥 

若要从 Key Vault 指定密钥，请执行以下步骤：

1. 选择“从 Key Vault 中选择”选项。  
2. 选择包含要使用的密钥的 Key Vault。
3. 从 Key Vault 中选择密钥。

    ![显示加密功能中使用自己密钥选项的门户屏幕截图](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

如果存储帐户无权访问 Key Vault ，则可以运行下图中显示的 Azure PowerShell 命令来授予访问权限。

![显示拒绝访问 Key Vault 的门户屏幕截图](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

此外，还可以通过 Azure 门户授予访问权限，方法是在 Azure 门户中导航到“Azure Key Vault”，然后向存储帐户授予访问权限。

### <a name="step-5-copy-data-to-storage-account"></a>步骤 5：将数据复制到存储帐户

若要将数据传输到新存储帐户中以便对其进行加密，请参阅[静态数据的存储服务加密入门](storage-service-encryption.md#step-3-copy-data-to-storage-account)中所述的步骤 3。

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>步骤 6：查询加密数据的状态

若要查询加密数据的状态，请参阅[静态数据的存储服务加密入门](storage-service-encryption.md#step-4-query-the-status-of-the-encrypted-data)中所述的步骤 4。

## <a name="faq-for-sse-with-customer-managed-keys"></a>针对使用客户托管密钥的 SSE 的常见问题解答

问：我正在使用高级存储；我是否可以将客户托管密钥用于 SSE？

答：可以，标准存储和高级存储都支持将 Microsoft 托管密钥和客户托管密钥用于 SSE。

问：是否可以在启用使用客户托管密钥的 SSE 的情况下，使用 Azure PowerShell 和 Azure CLI 创建新存储帐户？

答：是的。

问：如果将客户托管密钥用于 SSE，Azure 存储的费用将会高多少？

答：会产生 Azure Key Vault 相关的使用费。 有关详细信息，请访问[ Key Vault 定价](https://azure.microsoft.com/pricing/details/key-vault/)。 对所有存储帐户都启用的 SSE 不收取额外费用。

**问：是否可以吊销加密密钥的访问权限？**

答：是，随时可以吊销访问权限。 可通过多种方式吊销对密钥的访问权限。 有关详细信息，请参阅 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) 和 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 吊销访问权限会实际阻止对存储帐户中所有 Blob 的访问权限，因为 Azure 存储帐户无法访问帐户加密密钥。

**问：是否可以在不同的区域创建存储帐户和密钥？**

答：不可以，存储帐户和 Azure Key Vault 和密钥需在同一个区域。

问：创建存储帐户时是否可以启用使用客户托管密钥的 SSE？

答：没有。 首次创建存储帐户时，SSE 只能使用 Microsoft 托管的密钥。 若要使用客户托管的密钥，必须更新存储帐户属性。 可以使用 REST 或某个存储客户端库以编程方式更新存储帐户，或者在创建帐户后使用 Azure 门户更新存储帐户属性。

问：将客户托管密钥用于 SSE 时是否可以禁用加密？

答：不可以，不能禁用加密。 默认情况下，所有服务（Blob、文件、表和队列存储）都启用了加密。 可以选择从使用 Microsoft 托管的密钥转换为使用客户托管的密钥，反之亦然。

**问：创建新的存储帐户时是否会按默认启用 SSE？**

答：默认情况下，所有存储帐户和所有服务（Blob、文件、表和队列存储）都启用了 SSE。

问：我无法在存储帐户上启用使用客户托管密钥的 SSE。

答：帐户是 Azure 资源管理器存储帐户吗？ 客户托管的帐户不支持经典存储帐户。 仅可在资源管理器存储帐户上启用使用客户托管密钥的 SSE。

**问：什么是软删除和不清除？是否需要启用此设置以将客户托管密钥用于 SSE？**

答：必须启用软删除和不清除功能才能将客户托管密钥用于 SSE。 这些设置可确保密钥不被意外或有意地删除。 密钥的最大保持期设置为 90 天，保护用户免受恶意执行组件和勒索软件攻击。 无法禁用此设置。

问：是否只有特定的区域允许使用客户托管密钥的 SSE？

答：使用客户托管密钥的 SSE 在 Blob 和文件存储的所有区域均可用。

**问：如果遇到问题或想要提供反馈时怎样进行联系？**

答：任何有关存储服务加密的问题，请联系 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)。

## <a name="next-steps"></a>后续步骤

-   有关可帮助开发人员构建安全应用程序的综合安全功能集的详细信息，请参阅[存储安全指南](storage-security-guide.md)。

-   有关 Azure Key Vault的概述信息，请参阅[什么是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)

-   有关 Azure Key Vault 的入门信息，请参阅 [Azure Key Vault 入门](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)。

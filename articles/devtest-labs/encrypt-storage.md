---
title: 加密 Azure 开发测试实验室中的实验室使用的 Azure 存储帐户
description: 了解如何在 Azure 开发测试实验室中配置实验室使用的 Azure 存储的加密
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: 3c1d1531084deeabbe9a8d261e93554a2c691eb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87433537"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>加密 Azure 开发测试实验室中的实验室使用的 Azure 存储
使用关联的 Azure 存储帐户创建在 Azure 开发测试实验室中创建的每个实验室。 存储帐户用于以下目的： 

- 存储可用于创建虚拟机的 [公式](devtest-lab-manage-formulas.md) 文档。
- 存储项目结果，其中包括应用项目生成的部署和扩展日志。 
- [将虚拟硬盘上载 (Vhd) 在实验室中创建自定义映像。](devtest-lab-create-template.md)
- 缓存经常使用的 [项目](add-artifact-vm.md) 和 [Azure 资源管理器模板](devtest-lab-create-environment-from-arm.md) 以便在创建虚拟机/环境期间更快地检索。

> [!NOTE]
> 上述信息对实验室的操作至关重要。 它在实验室 (和实验室资源) 中存储，除非显式删除。 手动删除这些资源可能会导致创建实验室 Vm 和/或公式损坏以供将来使用的错误。 

## <a name="locate-the-storage-account-and-view-its-contents"></a>查找存储帐户并查看其内容

1. 在实验室的 "主页" 页上，选择 "**概述**" 页上的**资源组**。 应会看到包含实验室的资源组的 " **资源组** " 页。 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="在 &quot;概述&quot; 页上选择资源组":::
1. 选择实验室的 Azure 存储帐户。 实验室存储帐户的命名约定为： `a<labNameWithoutInvalidCharacters><4-digit number>` 。 例如，如果实验室名称为 `contosolab` ，则存储帐户名称可能是 `acontosolab7576` 。 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="在 &quot;概述&quot; 页上选择资源组" 以查找相关的与实验室相关的内容。 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="在 &quot;概述&quot; 页上选择资源组" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>加密实验室存储帐户
当数据保存到云时，Azure 存储会自动加密数据。 Azure 存储加密可保护你的数据，并可帮助你满足组织的安全性和符合性承诺。 有关详细信息，请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)。

实验室存储帐户中的数据使用 **Microsoft 托管的密钥**进行加密。 可以依赖于使用 Microsoft 托管的密钥来加密数据，也可以使用你自己的密钥来管理加密。 如果你选择使用你自己的密钥来管理实验室的存储帐户的加密，则可以使用 Azure Key Vault 来指定 **客户托管的密钥** ，以便在 Blob 存储和 Azure 文件中对数据进行加密/解密。 有关客户管理的密钥的详细信息，请参阅 [使用客户管理的密钥与 Azure Key Vault 管理 Azure 存储加密](../storage/common/encryption-customer-managed-keys.md)。

若要了解如何配置用于 Azure 存储加密的客户托管密钥，请参阅以下文章： 

- [Azure 门户](../storage/common/storage-encryption-keys-portal.md)
- [Azure PowerShell](../storage/common/storage-encryption-keys-powershell.md)
- [Azure CLI](../storage/common/storage-encryption-keys-cli.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>管理 Azure Blob 存储生命周期
如前所述，存储在实验室的存储帐户中的信息对于实验室进行操作至关重要，无需任何错误。 除非显式删除，否则，在实验室或特定实验室虚拟机的生命周期内，此数据将继续保留在实验室的存储帐户中，具体取决于数据类型。

### <a name="uploaded-vhds"></a>已上载 Vhd
这些 Vhd 用于创建自定义映像。 删除它们会使其无法再从这些 Vhd 创建自定义映像。

### <a name="artifacts-cache"></a>项目缓存
这些缓存将在应用时重新创建。 它们将用各自引用的存储库中的最新内容进行刷新。 因此，如果您删除此信息以节省存储相关的费用，则止裂止于暂时。

### <a name="azure-resource-manager-template-cache"></a>Azure 资源管理器模板缓存
在实验室中连接并启动 Azure 资源管理器的模板存储库时，将重新创建这些缓存。 它们将用各自引用的存储库中的最新内容进行刷新。 因此，如果您删除此信息以节省存储相关的费用，则止裂止于暂时。

### <a name="formulas"></a>公式
这些文档用于支持从现有 Vm 创建公式以及从公式创建 Vm 的选项。 在执行以下操作时，删除这些公式文档可能会导致错误：

- 从现有实验室 VM 创建公式
- 创建或更新公式 
- 从公式创建 VM。

### <a name="artifact-results"></a>项目结果
应用项目时，相应项目结果的大小会随时间增加，具体取决于在实验室 Vm 上运行的项目的数量和类型。 因此，作为实验室所有者，你可能想要控制此类文档的生命周期。 有关详细信息，请参阅[管理 Azure Blob 存储生命周期](../storage/blobs/storage-lifecycle-management-concepts.md)。

> [!IMPORTANT]
> 建议你执行此步骤以减少与 Azure 存储帐户相关的费用。 

例如，下面的规则用于设置专用于项目结果的90天过期规则。 它可确保旧的项目结果会定期从存储帐户回收。

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤
若要了解如何配置用于 Azure 存储加密的客户托管密钥，请参阅以下文章： 

- [Azure 门户](../storage/common/storage-encryption-keys-portal.md)
- [Azure PowerShell](../storage/common/storage-encryption-keys-powershell.md)
- [Azure CLI](../storage/common/storage-encryption-keys-cli.md)



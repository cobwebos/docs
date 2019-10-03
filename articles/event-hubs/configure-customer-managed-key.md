---
title: 为静态 Azure 事件中心数据配置你自己的密钥
description: 本文提供了有关如何配置自己的密钥来加密 Azure 事件中心数据 rest 的信息。
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: spelluru
ms.openlocfilehash: 37ca2b655d30ffd330d5430da20d07d9548a7c84
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260877"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>配置客户托管的密钥，以便通过使用 Azure 门户来加密静态 Azure 事件中心数据
Azure 事件中心通过 Azure 存储服务加密（Azure SSE）提供静态数据的加密。 事件中心依赖于 Azure 存储来存储数据，默认情况下，使用 Microsoft 托管密钥对存储在 Azure 存储中的所有数据进行加密。 

## <a name="overview"></a>概述
Azure 事件中心现在支持通过 Microsoft 管理的密钥或客户托管的密钥（创建自己的密钥– BYOK）来加密静态数据。 此功能使你能够创建、轮换、禁用和撤消对用于静态 Azure 事件中心数据加密的客户托管密钥的访问权限。

启用 BYOK 功能只是在命名空间上进行一次设置。

> [!NOTE]
> BYOK 功能受[事件中心专用单租户](event-hubs-dedicated-overview.md)群集支持。 不能为标准事件中心命名空间启用此功能。

你可以使用 Azure Key Vault 来管理密钥并审核密钥用法。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../key-vault/key-vault-overview.md)

本文介绍如何使用 Azure 门户配置包含客户管理密钥的密钥保管库。 若要了解如何使用 Azure 门户创建密钥保管库，请参阅 [] 快速入门：使用 Azure 门户] （...）设置和检索 Azure Key Vault 的机密/key-vault/quick-create-portal.md).

> [!IMPORTANT]
> 通过 Azure 事件中心使用客户托管的密钥，要求密钥保管库配置了两个必需的属性。 它们分别是：**软删除**并不**清除**。 在 Azure 门户中创建新的 Key Vault 时，默认会启用这些属性。 但是，如果需要针对现有的 Key Vault 启用这些属性，必须使用 PowerShell 或 Azure CLI。

## <a name="enable-customer-managed-keys"></a>启用客户管理的密钥
若要在 Azure 门户中启用客户管理的密钥，请执行以下步骤：

1. 导航到事件中心专用层群集。
1. 选择要在其上启用 BYOK 的命名空间。
1. 在事件中心命名空间的 "**设置**" 页上，选择 "**加密（预览）** "。 
1. 选择**客户托管的密钥加密**，如下图所示。 

    ![启用客户托管的密钥](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>使用密钥设置密钥保管库
启用客户管理的密钥后，需要将客户托管密钥与 Azure 事件中心命名空间相关联。 事件中心仅支持 Azure Key Vault。 如果在上一节中启用 "**使用客户管理的密钥加密**" 选项，则需要将密钥导入 Azure Key Vault。 此外，这些密钥必须包含**软删除**，并且**不会清除**密钥。 可以使用[PowerShell](../key-vault/key-vault-soft-delete-powershell.md)或[CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)配置这些设置。

1. 若要创建新的密钥保管库，请按照 Azure Key Vault[快速入门](../key-vault/key-vault-overview.md)中的步骤进行操作。 有关导入现有密钥的详细信息，请参阅[关于密钥、机密和证书](../key-vault/about-keys-secrets-and-certificates.md)。
1. 若要在创建保管库时启用软删除和清除保护，请使用[az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)命令。

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 若要将清除保护添加到现有保管库（已启用软删除），请使用[az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)命令。

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 按照以下步骤创建密钥：
    1. 若要创建新密钥，请从“设置”下的“密钥”菜单中选择“生成/导入”。
        
        ![选择 "生成/导入" 按钮](./media/configure-customer-managed-key/select-generate-import.png)
    1. 将“选项”设置为“生成”并提供密钥名称。

        ![创建密钥](./media/configure-customer-managed-key/create-key.png) 
    1. 你现在可以从下拉列表中选择此密钥以与用于加密的事件中心命名空间相关联。 

        ![从 key vault 中选择密钥](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. 填写密钥的详细信息，并单击 "**选择**"。 这将允许使用客户管理的密钥加密命名空间上的静态数据。 

        > [!NOTE]
        > 对于预览，只能选择一个密钥。 

## <a name="rotate-your-encryption-keys"></a>旋转加密密钥
可以通过使用 Azure 密钥保管库旋转机制，在密钥保管库中轮换密钥。 有关详细信息，请参阅[设置密钥轮替和审核](../key-vault/key-vault-key-rotation-log-monitoring.md)。 激活和到期日期还可以设置为自动执行密钥轮换。 事件中心服务将检测新的密钥版本，并自动开始使用它们。

## <a name="revoke-access-to-keys"></a>撤消对密钥的访问权限
撤消对加密密钥的访问权限不会清除事件中心的数据。 但是，无法从事件中心命名空间访问数据。 可以通过访问策略或通过删除密钥来撤消加密密钥。 了解有关访问策略的详细信息，以及如何保护密钥保管库免受[对密钥保管库的安全访问](../key-vault/key-vault-secure-your-key-vault.md)。

撤消加密密钥后，已加密命名空间上的事件中心服务将无法操作。 如果启用了对密钥的访问权限，或者还原了删除密钥，事件中心服务将选取密钥，以便可以从加密的事件中心命名空间访问数据。

> [!NOTE]
> 如果从密钥保管库中删除现有加密密钥，并将其替换为事件中心命名空间上的新密钥，因为删除密钥在长达一小时内仍有效（缓存），因此仍可访问旧数据（使用旧密钥加密） 使用新数据，现在只能使用新密钥进行访问。 此行为是在此功能的预览版本中设计的。 

## <a name="set-up-diagnostic-logs"></a>设置诊断日志 
为启用了 BYOK 的命名空间设置诊断日志，可以在使用客户管理的密钥对命名空间进行加密时，提供有关操作的必需信息。 可以启用这些日志，稍后将其流式传输到事件中心，或通过 log analytics 进行分析或流式传输到存储，以执行自定义的分析。 若要了解有关诊断日志的详细信息，请参阅[Azure 诊断日志概述](../azure-monitor/platform/resource-logs-overview.md)。

## <a name="enable-user-logs"></a>启用用户日志
按照以下步骤为客户管理的密钥启用日志。

1. 在 Azure 门户中，导航到已启用 BYOK 的命名空间。
1. 选择 "**监视**" 下的**诊断设置**。

    ![选择诊断设置](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. 选择 " **+ 添加诊断设置**"。 

    ![选择 "添加诊断设置"](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. 提供一个**名称**，并选择要将日志流式传输到的位置。
1. 选择**CustomerManagedKeyUserLogs**并**保存**。 此操作可在命名空间上启用 BYOK 的日志。

    ![选择客户托管的密钥用户日志选项](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>日志架构 
所有日志均以 JavaScript 对象表示法 (JSON) 格式存储。 每个条目都具有使用下表中描述的格式的字符串字段。 

| 姓名 | 描述 |
| ---- | ----------- | 
| TaskName | 失败的任务的说明。 |
| ActivityId | 用于跟踪的内部 ID。 |
| category | 定义任务的分类。 例如，如果密钥保管库中的密钥处于禁用状态，则它将是信息类别; 如果密钥无法解包，则可能发生错误。 |
| resourceId | Azure 资源管理器资源 ID |
| KeyVault | 密钥保管库的完整名称。 |
| 钥 | 用于加密事件中心命名空间的密钥名称。 |
| version | 所使用的密钥的版本。 |
| 操作 | 对密钥保管库中的密钥执行的操作。 例如，禁用/启用密钥、包装或解包 |
| code | 与操作关联的代码。 例如：错误代码404表示找不到键。 |
| 消息 | 与操作关联的任何错误消息 |

下面是客户托管密钥的日志示例：

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="troubleshoot"></a>故障排除
最佳做法是始终启用日志，如前一部分中所示。 启用 BYOK 加密后，它有助于跟踪活动。 它还有助于限制问题的范围。

下面是启用 BYOK 加密时要查找的常见错误代码。

| 操作 | 错误代码 | 数据的生成状态 |
| ------ | ---------- | ----------------------- | 
| 从密钥保管库中删除包装/解包权限 | 403 |    无法访问 |
| 从授予了 "包装/解包" 权限的 AAD 主体中删除 AAD 角色成员身份 | 403 |  无法访问 |
| 从密钥保管库中删除加密密钥 | 404 | 无法访问 |
| 删除密钥保管库 | 404 | 无法访问（假定启用软删除，这是必需的设置。） |
| 更改加密密钥的过期期限，使其已过期 | 403 |   无法访问  |
| 更改 NBF （而不是之前）以使密钥加密密钥不处于活动状态 | 403 | 无法访问  |
| 为 key vault 防火墙选择 "**允许 MSFT 服务**" 选项，或阻止对具有加密密钥的密钥保管库进行网络访问 | 403 | 无法访问 |
| 将密钥保管库移到其他租户 | 404 | 无法访问 |  
| 间歇性网络问题或 DNS/AAD/MSI 中断 |  | 可使用缓存的数据加密密钥进行访问 |

> [!IMPORTANT]
> 若要在使用 BYOK 加密的命名空间上启用异地灾难恢复，辅助命名空间必须在专用群集中，并且必须在该群集上启用系统分配的托管标识。 若要了解详细信息，请参阅[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

> [!NOTE]
> 如果在事件中心命名空间 Azure Key Vault 上配置了虚拟网络（VNet）服务终结点，则将不支持 BYOK。 


## <a name="next-steps"></a>后续步骤
请参阅以下文章：
- [事件中心概述](event-hubs-about.md)
- [Key Vault 概述](../key-vault/key-vault-overview.md)





---
title: 在 Azure 门户中创建帐户
description: 了解如何在 Azure 门户中创建 Azure Batch 帐户，以便在云中运行大规模并行工作负荷
ms.topic: how-to
ms.date: 02/26/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6cccef176e3e5ba0f4774a5897f082c4847a4005
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800252"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>使用 Azure 门户创建 Batch 帐户

了解如何在 [Azure 门户][azure_portal]中创建 Azure Batch 帐户，以及如何选择适合计算方案的帐户属性。 了解在何处查找重要的帐户属性，例如访问密钥和帐户 URL。

有关 Batch 帐户和方案的背景知识，请参阅 [Batch 服务工作流和资源](batch-service-workflow-features.md)。

## <a name="create-a-batch-account"></a>创建批处理帐户

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. 登录 [Azure 门户][azure_portal]。

1. 选择“创建资源” > “计算” > “Batch 服务”。

    ![市场中的批处理][marketplace_portal]

1. 输入“新 Batch 帐户”设置。 查看以下详细信息。

    ![创建批处理帐户][account_portal]

    a. **订阅**：要在其中创建 Batch 帐户的订阅。 如果只有一个订阅，则默认选择此项。

    b. **资源组**：为新 Batch 帐户选择现有的资源组，或选择创建一个新组。

    c. **帐户名**：所选名称必须在创建帐户的 Azure 区域中唯一（参见下面的“位置”）。 帐户名只能包含小写字符或数字，且长度必须为 3-24 个字符。

    d. **位置**：要在其中创建 Batch 帐户的 Azure 区域。 只有订阅和资源组支持的区域显示为选项。

    e. **存储帐户**：与 Batch 帐户关联的可选 Azure 存储帐户。 为获得最佳性能，建议使用常规用途 v2 存储帐户。 有关 Batch 中的所有存储帐户选项，请参阅 [Batch 功能概述](accounts.md#azure-storage-accounts)。 在门户中选择现有存储帐户，或者创建一个新帐户。

      ![创建存储帐户][storage_account]

    f. **池分配模式**：在“高级”设置选项卡中，可将池分配模式指定为“Batch 服务”或“用户订阅”  。 对于大多数情况，请接受默认值“Batch 服务”。

      ![Batch 池分配模式][pool_allocation]

1. 选择“创建”可创建帐户。

## <a name="view-batch-account-properties"></a>查看 Batch 帐户属性

创建帐户后，选择该帐户即可访问其设置和属性。 可以使用左侧菜单访问所有帐户设置和属性。

> [!NOTE]
> Batch 帐户名是它的 ID，无法更改。 如果需要更改 Batch 帐户名，则需要删除帐户，然后新建一个具有所需名称的帐户。

![Azure 门户中的 Batch 帐户页][account_blade]

* **Batch 帐户名、URL 和密钥**：通过 [Batch API](batch-apis-tools.md#azure-accounts-for-batch-development) 开发应用程序时，需要帐户 URL 和密钥才能访问 Batch 资源。 （Batch 还支持 Azure Active Directory 身份验证。）

    若要查看 Batch 帐户访问信息，请选择“密钥”。

    ![Azure 门户中的 Batch 帐户密钥][account_keys]

* 若要查看与 Batch 帐户关联的存储帐户的名称和密钥，请选择“存储帐户”。

* 若要查看适用于 Batch 帐户的资源配额，请选择“配额”。 有关详细信息，请参阅 [Batch 服务配额和限制](batch-quota-limit.md)。

## <a name="additional-configuration-for-user-subscription-mode"></a>用户订阅模式的其他配置

如果选择在用户订阅模式下创建 Batch 帐户，请在创建帐户前执行以下附加步骤。

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>允许 Azure Batch 访问订阅（一次性操作）

在用户订阅模式下创建第一个 Batch 帐户时，需将订阅注册到 Batch 中。 （如果已执行过此操作，请跳至下一部分。）

1. 登录 [Azure 门户][azure_portal]。

1. 选择“所有服务” > “订阅”，然后选择要用于 Batch 帐户的订阅。

1. 在“订阅”页中选择“资源提供程序”，然后搜索“Microsoft.Batch”。 查看 **Microsoft.Batch** 资源提供程序是否已在订阅中注册。 如果未注册，请选择“注册”链接。

    ![注册 Microsoft.Batch 提供程序][register_provider]

1. 在“订阅”页上，选择“访问控制(IAM)” > “角色分配” > “添加角色分配”。

    ![订阅访问控制][subscription_access]

1. 在“添加角色分配”页上，选择“参与者”角色，然后搜索 Batch API 。 搜索每一条字符串，直到找到此 API：
    1. MicrosoftAzureBatch。
    1. Microsoft Azure Batch。 较新的 Azure AD 租户可能使用此名称。
    1. ddbf3205-c6bd-46ae-8127-60eb93363864 是此 Batch API 的 ID。

1. 找到此 Batch API 后，将其选中，然后选择“保存”。

    ![添加批处理权限][add_permission]

### <a name="create-a-key-vault"></a>创建 key vault

在“用户订阅”模式下，需要的 Azure 密钥保管库与要创建的批处理帐户属于同一资源组。 请确保资源组所在的区域是[提供](https://azure.microsoft.com/regions/services/)批处理的区域，也是订阅所支持的区域。

1. 在 [Azure 门户][azure_portal]中，依次选择“新建” > “安全性” > “Key Vault”。

1. 在“创建密钥保管库”页中，输入密钥保管库的名称，并在区域中创建需要用于 Batch 帐户的资源组。 让其余设置保留默认值，然后选择“创建”。

在用户订阅模式下创建 Batch 帐户时，请使用密钥保管库的资源组。 指定“用户订阅”作为池分配模式，选择密钥保管库，然后选中框以向 Azure Batch 授予对密钥保管库的访问权限。 

若要手动授予对密钥保管库的访问权限，请转到密钥保管库的“访问策略”部分，选择“添加访问策略”，然后搜索“Microsoft Azure Batch”。 选择后，就需要使用下拉菜单来配置“机密权限”。 至少必须向 Azure Batch 授予“获取”、“列出”、“设置”和“删除”权限。

![Azure Batch 的机密权限](./media/batch-account-create-portal/secret-permissions.png)


> [!NOTE]
> 请务必在“访问策略”下为链接的“Key Vault”资源选中“用于部署的 Azure 虚拟机”和“用于模板部署的 Azure 资源管理器”复选框。
> 
> ![强制性 Key Vault 访问策略](./media/batch-account-create-portal/key-vault-access-policy.png) 在 Azure 门户中创建 Batch 帐户时，这不是强制性的。 此选项默认处于选中状态。



### <a name="configure-subscription-quotas"></a>配置订阅配额

默认情况下，不在用户订阅 Batch 帐户上设置核心配额。 核心配额必须手动设置，因为标准 Batch 核心配额不适用于用户订阅模式下的帐户。

1. 在 [Azure 门户][azure_portal]中，选择用户订阅模式 Batch 帐户，以显示它的设置和属性。

1. 在左侧菜单中选择“配额”，以便查看和配置与 Batch 帐户相关联的核心配额。

有关用户订阅模式核心配额的详细信息，请参阅 [Batch 服务的配额和限制](batch-quota-limit.md)。

## <a name="other-batch-account-management-options"></a>其他 Batch 帐户管理选项

除了使用 Azure 门户外，还可使用以下工具创建和管理 Batch 帐户：

* [批处理 PowerShell cmdlet](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>后续步骤

* 了解 [Batch 服务工作流和主要资源](batch-service-workflow-features.md)（如池、节点、作业和任务）。
* 了解使用[批处理 .NET 客户端库](quick-run-dotnet.md)或 [Python](quick-run-python.md) 开发支持批处理的应用程序的基本概念。 这些快速入门介绍了使用 Batch 服务在多个计算节点上执行工作负荷的示例应用程序，并说明了如何使用 Azure 存储进行工作负荷文件暂存和检索。

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png

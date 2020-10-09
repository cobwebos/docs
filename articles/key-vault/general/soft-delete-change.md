---
title: 对所有 Azure Key Vault 启用软删除 | Microsoft Docs
description: 使用本文档对所有 Key Vault 采用软删除。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: sudbalas
ms.openlocfilehash: 0e811cc219002c034afb968be760ce2c249b08f3
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825247"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>对所有 Key Vault 启用软删除

> [!WARNING]
> 中断性变更：选择退出软删除功能将于今年年底弃用，所有 Key Vault 的软删除保护将自动启用。  Azure Key Vault 用户和管理员应立即对其 Key Vault 启用软删除。
>
> 对于托管 HSM，默认情况下启用软删除，并且无法禁用。

如果在没有软删除保护的情况下从 Key Vault 中删除机密，则该机密将被永久删除。 用户当前可以在创建 Key Vault 期间选择退出软删除，但是为了防止用户意外或恶意删除你的机密，Microsoft 不久将对所有 Key Vault 启用软删除保护，而用户将不再能够选择退出或关闭软删除。

:::image type="content" source="../media/softdeletediagram.png" alt-text="<替换文字>":::

有关软删除功能的完整详细信息，请参阅 [Azure Key Vault 软删除概述](soft-delete-overview.md)。

## <a name="how-do-i-respond-to-breaking-changes"></a>我如何应对中断性变更

不能创建与“已软删除”状态下的 Key Vault 对象同名的 Key Vault 对象。  例如，如果你在 Key Vault A 中删除名为 `test key` 的密钥，则在已软删除的 `test key` 对象被清除之前，你无法在 Key Vault A 中创建名为 `test key` 的新密钥。

### <a name="application-changes"></a>应用程序更改

如果应用程序假设未启用软删除，并且希望已删除的机密或 Key Vault 名称可立即重复使用，则应用程序逻辑将需要进行以下更改才能采用此更改。

1. 删除原始 Key Vault 或机密
2. 清除“已软删除”状态下的 Key Vault 或机密。
3. 等待 - 立即重新创建可能会导致冲突。
4. 重新创建具有相同名称的 Key Vault。
5. 如果创建操作仍然导致名称冲突错误，请实现重试，在最坏的情况下，DNS 记录更新最多可能需要 10 分钟。

### <a name="administration-changes"></a>管理更改

需要访问永久删除机密的安全主体必须获授额外的访问策略权限才能清除这些机密和 Key Vault。

如果你对 Key Vault 实施了一个要求关闭软删除的 Azure Policy，则需要禁用此策略。  你可能需要将此问题上报给控制应用于环境的 Azure Policy 的管理员。 如果未禁用此策略，你可能无法在所应用策略范围内创建新的 Key Vault。

如果你的组织受到法律合规性要求的约束，并且不允许已删除的 Key Vault 和机密长时间保持可恢复状态，则你必须调整软删除的保持期，该期限可以在 7 到 90 天之间进行配置，以满足你组织的标准。

## <a name="procedures"></a>过程

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>审核 Key Vault，检查是否启用了软删除

1. 登录到 Azure 门户。
2. 搜索“Azure Policy”。
3. 选择“定义”。
4. 在“类别”下，选择筛选器中的“Key Vault”。
5. 选择“Key Vault 对象应可恢复”策略。
6. 单击“分配”。
7. 将范围设置为你的订阅。
8. 选择“查看 + 创建”。
9. 最多需要 24 小时才能完成环境的完整扫描。
10. 在“Azure Policy”边栏选项卡中，单击“合规性”。
11. 选择应用的策略。

现在，你应该能够筛选并查看启用了软删除的 Key Vault（合规资源）以及未启用软删除的 Key Vault（不合规资源）。

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>对现有的 Key Vault 启用软删除

1. 登录到 Azure 门户。
2. 搜索 Key Vault。
3. 选择设置下的“属性”。
4. 在“软删除”下，选择与“启用对此保管库及其对象的恢复”。
5. 设置软删除的保持期。
6. 选择“保存”。

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>向安全主体授予清除访问策略权限

1. 登录到 Azure 门户。
2. 搜索 Key Vault。
3. 选择设置下的“访问策略”。
4. 选择要授予访问权限的服务主体。
5. 对于密钥、机密和证书权限下的每个下拉列表，请向下滚动到“特权操作”，然后选择“清除”权限。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="does-this-change-affect-me"></a>此更改是否会对我产生影响？

如果你已启用软删除，或者没有删除并重新创建同名的 Key Vault 对象，则可能不会注意到 Key Vault 的行为发生任何更改。

如果你的应用程序经常删除和重新创建具有相同命名约定的 Key Vault 对象，则你必须在应用程序逻辑中进行更改，以保持预期的行为。 请参阅上面的“我如何应对中断性变更？” 一节。

### <a name="how-do-i-benefit-from-this-change"></a>我如何从这一更改中获益？

软删除保护将为你的组织提供额外的保护层，以防止意外或恶意删除。 Key Vault 管理员可以限制对恢复权限和清除权限的访问。

如果用户意外地删除了 Key Vault 或机密，你可以授予他们访问权限以自行恢复机密，而不会造成他们永久删除机密或 Key Vault 的风险。 此自助式过程将最大程度地减少环境中的停机时间，并保证机密的可用性。

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>如何确定我是否需要采取措施？

请按照上面标题为“审核 Key Vault 以检查软删除是否已启用的过程”一节中的步骤进行操作。 任何未启用软删除的 Key Vault 都将受到此更改的影响。 其他有助于审核的工具将很快推出，本文档将进行更新。

### <a name="what-action-do-i-need-to-take"></a>我需要采取哪些措施？

确保无需更改应用程序逻辑。 确认后，对所有 Key Vault 启用软删除。 这样可以确保在年底对所有 Key Vault 启用软删除时，你不会受到中断性变更的影响。

### <a name="by-when-do-i-need-to-take-action"></a>什么时候需要措施？

今年年底我们将对所有 Key Vault 启用软删除。 为确保你的应用程序不受影响，请尽快对 Key Vault 启用软删除。

## <a name="what-will-happen-if-i-dont-take-any-action"></a>如果我不采取任何措施将会怎样？

如果你不采取任何措施，则到今年年底我们将对所有 Key Vault 自动启用软删除。 如果你尝试删除一个 Key Vault 对象并使用相同的名称重新创建它，而不先将其从“已软删除”状态中清除，这可能会导致冲突错误。 这可能会导致应用程序或自动化失败。

## <a name="next-steps"></a>后续步骤

- 如有任何关于此更改的问题，请通过 [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) 与我们联系。
- 阅读[软删除概述](soft-delete-overview.md)

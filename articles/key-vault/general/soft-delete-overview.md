---
title: Azure Key Vault 软删除 |Microsoft Docs
description: 使用 Azure Key Vault 软删除可恢复已删除的密钥保管库和密钥保管库对象，例如密钥、机密和证书。
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: 1affa396407ba9804261c799b559e40928b9b1fa
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388361"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Azure Key Vault 软删除概述

> [!IMPORTANT]
> 必须立即对密钥保管库启用软删除。 选择退出软删除功能的功能将在一年结束后弃用，并且将自动为所有 key vault 启用软删除保护。  [在此处](soft-delete-change.md)查看完整的详细信息

Key Vault 的软删除功能可以恢复已删除的保管库，并删除已删除的密钥保管库对象（例如密钥、机密和证书），这称为软删除。 具体而言，我们要处理以下方案：此保护提供以下保护：

- 删除密钥、密钥、证书或密钥保管库后，它将在可配置的7到90日历天内保持可恢复。 如果未指定配置，则默认恢复时间将设置为90天。 这为用户提供了充足的时间来通知意外的机密删除和响应。
- 若要永久删除机密，必须执行两个操作。 首先，用户必须删除该对象，然后将其置于软删除状态。 其次，用户必须清除软删除状态的对象。 清除操作需要其他访问策略权限。 这些附加保护降低了用户意外或恶意删除密钥或密钥保管库的风险。  
- 若要清除软删除状态的机密，必须为服务主体授予额外的 "清除" 访问策略权限。 默认情况下，清除访问策略权限不会授予任何服务主体，包括密钥保管库和订阅所有者，并且必须特意设置。 如果要求提升的访问策略权限来清除软删除的机密，则会降低意外删除机密的概率。

## <a name="supporting-interfaces"></a>支持接口

软删除功能最初通过[REST](/rest/api/keyvault/)、 [CLI](soft-delete-cli.md)、 [PowerShell](soft-delete-powershell.md)和[.net/c #](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet)接口以及[ARM 模板](https://docs.microsoft.com/azure/templates/microsoft.keyvault/2019-09-01/vaults)提供。

## <a name="scenarios"></a>方案

Azure Key Vault 是由 Azure Resource Manager 管理的跟踪资源。 Azure Resource Manager 还指定了定义明确的删除行为，要求成功的删除操作必须使该资源不再可供访问。 软删除功能解决了已删除对象的恢复问题，无论是意外删除还是有意删除。

1. 在典型情景中，用户可能无意中删除了 Key Vault 或 Key Vault 对象；如果 Key Vault 或 Key Vault 对象在预设的某个时间段内可恢复，则用户可以撤消删除并恢复其数据。

2. 在另一种情景中，恶意用户可能会试图删除 Key Vault 或 Key Vault 对象（例如保管库内的密钥），导致业务中断。 作为一项安全措施，可将 Key Vault 或 Key Vault 对象的删除与基础数据的实际删除区分开来，例如，将数据删除权限仅授予另一个受信任角色。 此方法实际上需要对可能导致数据立即丢失的操作进行仲裁。

### <a name="soft-delete-behavior"></a>软删除行为

启用软删除后，标记为“已删除资源”的资源将保留指定的时间（默认为 90 天）。 该服务进一步提供了用于恢复已删除对象的机制，实质上是撤消删除。

创建新的密钥保管库时，默认情况下将启用软删除。 可以通过 [Azure CLI](soft-delete-cli.md) 或 [Azure PowerShell](soft-delete-powershell.md) 创建不带软删除的密钥保管库。 在密钥保管库上启用软删除后，便无法将其禁用

默认保留期为 90 天，但在创建密钥保管库期间可通过 Azure 门户将保留策略间隔设为 7 到 90 天的值。 清除保护保留策略使用相同的间隔。 设置保留策略间隔后，将无法更改。

在保留期结束之前，无法重复使用已软删除的 Key Vault 的名称。

### <a name="purge-protection"></a>清除保护 

清除保护是一种可选的 Key Vault 行为，**默认未启用**。 只有启用软删除后才能启用清除保护。  可以通过 [CLI](soft-delete-cli.md#enabling-purge-protection) 或 [PowerShell](soft-delete-powershell.md#enabling-purge-protection) 来启用它。

启用清除保护后，在保留期结束之前，无法清除处于已删除状态的保管库或对象。 软删除的保管库和对象仍可恢复，这可以确保遵循保留策略。 

默认保留期为 90 天，但可以通过 Azure 门户将保留策略间隔设置为 7 到 90 天的值。 设置并保存保留策略间隔后，无法为该保管库更改保留策略间隔。 

### <a name="permitted-purge"></a>允许的清除

可通过对代理资源执行 POST 操作永久删除、清除 Key Vault，但此操作需要特殊权限。 通常，只有订阅所有者才能清除 Key Vault。 POST 操作可触发立即删除该保管库，且此删除不可恢复。 

例外情况包括：
- Azure 订阅已被标记为“不可删除”。 在这种情况下，只有服务可以执行实际删除，并且将作为计划的进程执行此操作。 
- 在保管库本身上启用 `--enable-purge-protection flag` 时。 在这种情况下，Key Vault 将自原始机密对象标记为删除以永久删除该对象起等待 90 天。

### <a name="key-vault-recovery"></a>Key Vault 恢复

删除 Key Vault 后，服务会在订阅下创建代理资源，为恢复添加足够的元数据。 代理资源是一个存储对象，位于与已删除 Key Vault 相同的位置。 

### <a name="key-vault-object-recovery"></a>Key Vault 对象恢复

删除密钥保管库对象（例如密钥）时，服务会将该对象置于已删除状态，使其不可供任何检索操作访问。 在此状态下，只能列出、恢复或强制/永久删除 Key Vault 对象。 若要查看对象，请使用 Azure CLI `az keyvault key list-deleted` 命令（如[如何将 Key Vault 软删除与 CLI 配合使用](soft-delete-cli.md)中所述），或 Azure PowerShell `-InRemovedState` 参数（如[如何将 Key Vault 软删除与 PowerShell 一起使用](soft-delete-powershell.md#secrets)中所述）。  

同时，Key Vault 将计划在预设的保留间隔后删除与已删除 Key Vault 或 Key Vault 对象对应的基础数据。 在保留间隔内，还会保留与该保管库相对应的 DNS 记录。

### <a name="soft-delete-retention-period"></a>软删除保留期

软删除的资源将保留设定的一段时间（90 天）。 在软删除保留间隔内，以下情况属实：

- 可以列出订阅中处于软删除状态的所有 Key Vault 和 Key Vault 对象，并可访问与这些对象有关的删除和恢复信息。
    - 只有具有特殊权限的用户才能列出已删除的保管库。 我们建议用户创建一个具有这些特殊权限的自定义角色来处理已删除的保管库。
- 无法在同一位置创建具有相同名称的 Key Vault；相应地，在创建 Key Vault 对象时，如果 Key Vault 中包含具有相同名称且处于已删除状态的对象，则无法在其中创建该对象。 
- 只有特权用户可以还原 Key Vault 或 Key Vault 对象，方法是对相应的代理资源发出恢复命令。
    - 有权在资源组下创建 key vault 的用户（自定义角色的成员）可以还原该保管库。
- 只有特权用户可以强制删除 Key Vault 或 Key Vault 对象，方法是对相应的代理资源发出删除命令。

除非恢复 Key Vault 或 Key Vault 对象，否则在保留间隔结束时，服务会清除已软删除的 Key Vault 或 Key Vault 对象及其内容。 资源删除不可重新计划。

### <a name="billing-implications"></a>计费影响

一般情况下，如果对象（密钥保管库或密钥或机密）处于已删除状态，仅可执行两个操作：“清除”和“恢复”。 所有其他操作都会失败。 因此，即使对象存在，也不可执行任何操作，因此不会出现使用情况，也不会计费。 但是存在以下例外：

- “清除”和“恢复”操作计为正常密钥保管库操作并收费。
- 如果对象为 HSM 密钥，且在过去 30 天内使用过密钥版本，则会对“受 HSM 保护的密钥”按每月每个密钥版本收费。 之后，由于该对象处于已删除状态，无法对其执行任何操作，因此不会产生任何费用。

## <a name="next-steps"></a>后续步骤

以下两个指南提供有关使用软删除的主要使用方案。

- [如何将 Key Vault 软删除与 PowerShell 配合使用](soft-delete-powershell.md) 
- [如何将 Key Vault 软删除与 CLI 配合使用](soft-delete-cli.md)


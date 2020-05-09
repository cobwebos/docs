---
title: Azure Key Vault 软删除 |Microsoft Docs
description: Azure Key Vault 中的软删除可恢复已删除的密钥保管库和密钥保管库对象，例如密钥、机密和证书。
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: 347f8a4cf1fb95849bcf1008e91d17878f3d01f8
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598509"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Azure Key Vault 软删除概述

Key Vault 的软删除功能可以恢复已删除的保管库和保管库对象，称为软删除。 本文将具体探讨以下方案：

- 支持 Key Vault 的可恢复删除
- 支持 Key Vault 对象的可恢复删除（例如 密钥、机密和证书）

## <a name="supporting-interfaces"></a>支持接口

软删除功能最初通过[REST](/rest/api/keyvault/)、 [CLI](soft-delete-cli.md)、 [PowerShell](soft-delete-powershell.md)和[.net/c #](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet)接口以及[ARM 模板](https://docs.microsoft.com/azure/templates/microsoft.keyvault/2019-09-01/vaults)提供。

## <a name="scenarios"></a>方案

Azure Key Vault 是 Azure 资源管理器管理的跟踪资源。 Azure 资源管理器还指定了定义明确的删除行为，要求成功的 DELETE 操作必须使该资源不再可访问。 软删除功能解决了已删除对象的恢复问题，无论是意外删除还是有意删除。

1. 在典型方案中，用户可能无意中删除了 Key Vault 或 Key Vault 对象；如果 Key Vault 或 Key Vault 对象在预定时间段内可恢复，则用户可以撤消删除并恢复其数据。

2. 在不同的方案中，恶意用户可能会尝试删除 Key Vault 或 Key Vault 对象（例如保管库内的密钥），导致业务中断。 作为安全措施，可将 Key Vault 或 Key Vault 对象的删除与基础数据的实际删除区分开，例如，将数据删除的权限限制为不同的受信任角色。 此方法实际上需要对可能导致数据立即丢失的操作进行仲裁。

### <a name="soft-delete-behavior"></a>软删除行为

启用软删除后，标记为已删除资源的资源将保留指定的时间（默认为90天）。 该服务还提供用于恢复已删除对象的机制，从实质上撤销删除。

创建新的密钥保管库时，默认情况下启用软删除。 可以通过[Azure CLI](soft-delete-cli.md)或[Azure PowerShell](soft-delete-powershell.md)创建不包含软删除的密钥保管库。 在密钥保管库上启用软删除后，不能将其禁用

默认保持期为90天，但在创建密钥保管库期间，可以通过 Azure 门户将保留策略间隔设置为从7到90天的值。 清除保护保留策略使用相同的间隔。 设置后，不能更改保留策略间隔。

在保留期结束之前，无法重复使用已软删除的 Key Vault 的名称。

### <a name="purge-protection"></a>清除保护 

清除保护是一种可选的 Key Vault 行为，**默认未启用**。 启用软删除后，只能启用清除保护。  可以通过[CLI](soft-delete-cli.md#enabling-purge-protection)或[PowerShell](soft-delete-powershell.md#enabling-purge-protection)启用此功能。

启用清除保护后，在保留期结束之前，无法清除处于已删除状态的保管库或对象。 软删除的保管库和对象仍可恢复，这可以确保遵循保留策略。 

默认保留期为 90 天，但可以通过 Azure 门户将保留策略间隔设置为 7 到 90 天的值。 设置并保存保留策略间隔后，无法为该保管库更改保留策略间隔。 

### <a name="permitted-purge"></a>允许的清除

可通过代理资源上的 POST 操作永久删除、清除 Key Vault，但此操作需要特殊权限。 通常，只有订阅所有者才能清除 Key Vault。 POST 操作可触发立即删除该保管库，且此删除不可恢复。 

例外情况包括：
- Azure 订阅已被标记为“不可删除”。** 在这种情况下，只有服务可以执行实际删除，并且将作为计划的进程执行此操作。 
- `--enable-purge-protection flag`在保管库本身上启用。 在这种情况下，Key Vault 将自原始机密对象标记为删除以永久删除该对象起等待 90 天。

### <a name="key-vault-recovery"></a>Key Vault 恢复

删除 Key Vault 后，服务会在订阅下创建代理资源，为恢复添加足够的元数据。 代理资源是一个存储对象，位于与已删除 Key Vault 相同的位置。 

### <a name="key-vault-object-recovery"></a>Key Vault 对象恢复

删除密钥保管库对象（例如密钥）时，服务会将该对象置于已删除状态，使其不可供任何检索操作访问。 在此状态下，只能列出、恢复或强制/永久删除 Key Vault 对象。 

同时，Key Vault 将计划在预定的保留间隔后删除对应于已删除 Key Vault 或 Key Vault 对象的基础数据。 在保留间隔期间，还会保留与保管库相对应的 DNS 记录。

### <a name="soft-delete-retention-period"></a>软删除保留期

软删除的资源将保留一段时间，即90天。 以下项在软删除保留间隔期间适用：

- 可列出订阅中处于软删除状态的所有 Key Vault 和 Key Vault 对象，并可访问与这些对象有关的删除和恢复信息。
    - 只有具有特殊权限的用户才能列出已删除的保管库。 建议用户使用这些特殊权限创建自定义角色来处理已删除的保管库。
- 无法在同一位置创建具有相同名称的 Key Vault；相应地，如果 Key Vault 包含具有相同名称且处于已删除状态的对象，则无法在给定保管库中创建 Key Vault 对象 
- 只有特权用户可以还原 Key Vault 或 Key Vault 对象，方法是对相应的代理资源发出恢复命令。
    - 作为自定义角色的成员，用户有权在可还原保管库的资源组下创建 Key Vault。
- 只有特权用户可以强制删除 Key Vault 或 Key Vault 对象，方法是对相应的代理资源发出删除命令。

除非恢复 Key Vault 或 Key Vault 对象，否则在保留间隔结束时，服务将清除已软删除的 Key Vault 或 Key Vault 对象及其内容。 可能无法重新计划资源删除操作。

### <a name="billing-implications"></a>计费影响

一般情况下，如果对象（密钥保管库或密钥或机密）处于已删除状态，仅可执行两个操作：“清除”和“恢复”。 所有其他操作都会失败。 因此，即使对象存在，也不可执行任何操作，因此不会出现使用情况，也不会计费。 但是存在以下例外：

- “清除”和“恢复”操作计为正常密钥保管库操作并收费。
- 如果对象为 HSM 密钥，且在过去 30 天内使用过密钥版本，则会对“受 HSM 保护的密钥”按每月每个密钥版本收费。 之后，由于该对象处于已删除状态，无法对其执行任何操作，因此不会产生任何费用。

## <a name="next-steps"></a>后续步骤

以下两个指南提供有关使用软删除的主要使用方案。

- [如何将 Key Vault 软删除与 PowerShell 配合使用](soft-delete-powershell.md) 
- [如何将 Key Vault 软删除与 CLI 配合使用](soft-delete-cli.md)


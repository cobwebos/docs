---
ms.assetid: 
title: "Azure Key Vault 软删除 | Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 2b73fb55384cdcc8db2736557e0efef97b34fc45
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017

---
# <a name="azure-key-vault-soft-delete-feature-overview"></a>Azure Key Vault 软删除功能概述

>[!NOTE]
>在此预览版 Azure Key Vault 中，只有“软删除”功能处于预览状态。 Azure Key Vault 作为一个整体，是完整的生产服务。

此预览版 Azure Key Vault 描述了 Key Vault 和 Key Vault 对象的可恢复删除（称为“软删除”）。 本文将具体探讨以下方案：

- 支持 Key Vault 的可恢复删除
- 支持 Key Vault 对象的可恢复删除（例如 密钥、机密和证书）

## <a name="supporting-interfaces"></a>支持接口

软删除功能最初通过 REST、.NET/C# 和 PowerShell 接口提供。 有关详细信息，请参阅参考文献 [Key Vault 参考](https://docs.microsoft.com/azure/key-vault/)。

## <a name="scenarios"></a>方案

Azure Key Vault 是 Azure Resource Manager 管理的跟踪资源。 Azure Resource Manager 还指定了定义明确的删除行为，要求成功的 DELETE 操作必须使该资源不再可访问。 软删除功能解决了已删除对象的恢复问题，无论是意外删除还是有意删除。

1. 在典型方案中，用户可能无意中删除了 Key Vault 或 Key Vault 对象；如果 Key Vault 或 Key Vault 对象在预定时间段内可恢复，则用户可以撤消删除并恢复其数据。

2. 在不同的方案中，恶意用户可能会尝试删除 Key Vault 或 Key Vault 对象（例如保管库内的密钥），导致业务中断。 作为安全措施，可将 Key Vault 或 Key Vault 对象的删除与基础数据的实际删除区分开，例如，将数据删除的权限限制为不同的受信任角色。 这实际上需要对可能导致数据立即丢失的操作进行仲裁。

### <a name="soft-delete-behavior"></a>软删除行为

在此预览版中，Key Vault 或 Key Vault 对象上的 DELETE 操作是软删除，因此可以有效地在给定保留期内保留资源，同时通过外观提示已删除对象。 该服务还提供用于恢复已删除对象的机制，从实质上撤销删除。 

软删除是可选 Key Vault 行为，在此版本中默认未启用。 有关为 Key Vault 启用软删除的详细信息，请参阅所选接口 [Key Vault 参考](https://docs.microsoft.com/azure/key-vault/)文献中的具体指南。

### <a name="key-vault-recovery"></a>Key Vault 恢复

删除 Key Vault 后，服务将在订阅下创建代理资源，为恢复添加足够的元数据。 代理资源是一个存储对象，位于与已删除 Key Vault 相同的位置。 

### <a name="key-vault-object-recovery"></a>Key Vault 对象恢复

删除 Key Vault 对象（如密钥）后，服务会将该对象置于已删除状态，从而使其对任何检索操作均不可访问。 在此状态下，只能列出、恢复或强制/永久删除 Key Vault 对象。 

同时，Key Vault 将计划在预定的保留间隔后删除对应于已删除 Key Vault 或 Key Vault 对象的基础数据。 在保留间隔期间，还会保留与保管库相对应的 DNS 记录。

### <a name="soft-delete-retention-period"></a>软删除保留期

软删除的资源将保留一段时间（90 天）。 

>[!NOTE]
> 2017 年 5 月 10 日的预览版无法配置此软删除保留期。 

以下项在软删除保留间隔期间适用：

- 可列出订阅中处于软删除状态的所有 Key Vault 和 Key Vault 对象，并可访问与这些对象有关的删除和恢复信息。
    - 只有具有特殊权限的用户才能列出已删除的保管库。 建议用户使用这些特殊权限创建自定义角色来处理已删除的保管库。
- 无法在同一位置创建具有相同名称的 Key Vault；相应地，如果 Key Vault 包含具有相同名称且处于已删除状态的对象，则无法在给定保管库中创建 Key Vault 对象 
- 只有特权用户可以还原 Key Vault 或 Key Vault 对象，方法是对相应的代理资源发出恢复命令。
    - 作为自定义角色的成员，用户有权在可还原保管库的资源组下创建 Key Vault。
- 只有特权用户可以强制删除 Key Vault 或 Key Vault 对象，方法是对相应的代理资源发出删除命令。

除非恢复 Key Vault 或 Key Vault 对象，否则在保留间隔结束时，服务将清除已软删除的 Key Vault 或 Key Vault 对象及其内容。 可能无法重新计划资源删除操作。

### <a name="permissioned-purge"></a>授权清除

可通过代理资源上的 POST 操作永久删除、清除 Key Vault，但此操作需要特殊权限。 通常，只有订阅所有者才能清除 Key Vault。 POST 操作可触发立即删除该保管库，且此删除不可恢复。 

一种例外情况是，Azure 订阅已被标记为“不可删除”。 在这种情况下，只有服务可以执行实际删除，并且将作为计划的进程执行此操作。 





---
title: 在 Azure Sentinel 中设置客户管理的密钥 |Microsoft Docs
description: 了解如何在 Azure Sentinel 中设置客户管理的密钥（CMK）。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: rkarlin
ms.openlocfilehash: 4858e2099baa82226766ecfce32b39471c3b96cd
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845318"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>设置 Azure Sentinel 客户托管密钥


本文提供了有关为 Azure Sentinel 配置客户托管的密钥（CMK）的背景信息和步骤。 通过 CMK，可以在所有相关存储资源中使用创建或拥有 Azure Key Vault 密钥来加密保存或发送到 Azure Sentinel 的所有数据。

> [!NOTE]
> -   仅**向不熟悉的客户**提供 AZURE Sentinel CMK 功能，并通过 azure 功能注册来控制对此功能的访问。 你可以通过联系 azuresentinelCMK@microsoft.com来请求访问权限，并且在容量可用的情况下，将批准挂起的请求。
> -   仅在美国东部、美国西部2和美国中南部地区提供 Azure Sentinel CMK 功能。
> -   CMK 功能仅适用于每天发送1TB 或更多的客户。 当你应用到 Microsoft 以在 Azure 订阅上设置 CMK 时，你将收到有关其他定价的信息。 详细了解[Log Analytics](../azure-monitor/platform/customer-managed-keys.md#disclaimers)充电。

## <a name="how-cmk-works"></a>CMK 的工作原理 

Azure Sentinel 解决方案使用几个存储资源来提供日志收集和功能，其中包括 Log Analytics 和其他存储资源。 作为 Azure Sentinel CMK 配置的一部分，你还必须配置相关存储资源上的 CMK 设置。 保存在除 Log Analytics 以外的存储资源中的数据也将被加密。

> [!NOTE]
> 如果在 Azure Sentinel 上启用 CMK，则不支持 CMK 的任何公共预览功能都将不会启用。

## <a name="enable-cmk"></a>启用 CMK 

若要设置 CMK，请执行以下步骤： 

1.  创建 Azure Key Vault 并存储密钥。

2.  在 Log Analytics 工作区上启用 CMK。

3.  注册 Cosmos DB。

4.  向 Azure Key Vault 实例添加访问策略。

5.  在 Azure Sentinel 中启用 CMK。

6.  启用 Azure Sentinel。

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>步骤1：创建 Azure Key Vault 并存储密钥

1.  [创建 Azure Key Vault 资源](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910)，然后生成或导入用于数据加密的密钥。
    > [!NOTE]
    >  必须将 Azure Key Vault 配置为可恢复，以保护你的密钥和访问权限。

1.  [启用恢复选项：](../key-vault/key-vault-best-practices.md#turn-on-recovery-options)

    -   请确保已启用[软删除](../key-vault/key-vault-ovw-soft-delete.md)。

    -   启用[清除保护](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)，防止在软删除后强行删除密钥/保管库。

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>步骤2：在 Log Analytics 工作区上启用 CMK

按照[Azure Monitor 客户托管的密钥配置](../azure-monitor/platform/customer-managed-keys.md)中的说明，创建一个 CMK 工作区，该工作区将在以下步骤中用作 Azure Sentinel 工作区。

### <a name="step-3-register-for-cosmos-db"></a>步骤3：注册 Cosmos DB

Azure Sentinel 使用 Cosmos DB 作为附加存储资源。 请确保注册到 Cosmos DB。

按照 Cosmos DB 说明为你的 Azure 订阅[注册 Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider)资源提供程序。

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>步骤4：将访问策略添加到 Azure Key Vault 实例

请确保将 Cosmos DB 中的访问权限添加到 Azure Key Vault 实例。 按照 Cosmos DB 说明[将访问策略添加到](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)具有 Azure Cosmos DB 主体的 Azure Key Vault 实例。

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>步骤5：在 Azure Sentinel 中启用 CMK

仅当直接从 Azure 产品组接收访问权限后，才向新客户提供 Azure Sentinel CMK 功能。 使用 Microsoft 的联系人接收 Azure Sentinel 团队的批准，以便在你的解决方案中启用 CMK。

获得批准后，将要求你提供以下信息来启用 CMK 功能。

-  要在其上启用 CMK 的工作区 ID

-  Key Vault URL：将密钥的 "密钥标识符" 复制到最后一个正斜杠：  
    

    ![密钥标识符](./media/customer-managed-keys/key-identifier.png)

    Azure Sentinel 团队将为你提供的工作区启用 Azure Sentinel CMK 功能。

-  从 Azure Sentinel 产品团队验证你已批准使用此功能。 必须先进行此操作，然后才能继续。

### <a name="step-6-enable-azure-sentinel"></a>步骤6：启用 Azure Sentinel


请在设置 CMK 的工作区中转到 Azure 门户并启用 Azure Sentinel。 有关详细信息，请参阅[Azure Sentinel 载入](quickstart-onboard.md)。

## <a name="key-encryption-key-revocation-or-deletion"></a>密钥加密密钥吊销或删除


如果用户通过删除密钥加密密钥或在一小时内删除 Azure Sentinel 的访问权限，则 Azure Sentinel 将服从此更改，并表现为数据不再可用。 此时，将阻止执行使用持久性存储资源（如数据引入、持久配置更改和事件创建）的任何操作。 以前存储的数据将不会被删除，但仍将无法访问。 不可访问的数据由数据保留策略控制，并将根据该策略被清除。

撤消或删除加密密钥后，唯一可能的操作是删除帐户。

如果在吊销后恢复访问，Azure Sentinel 将在一小时内还原对数据的访问权限。

若要详细了解此操作在 Azure Monitor 中的工作原理，请参阅[AZURE MONITOR CMK 吊销](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation)。

## <a name="key-encryption-key-rotation"></a>密钥加密密钥轮换


Azure Sentinel 和 Log Analytics 支持密钥轮换。 当用户在 Key Vault 执行密钥轮换时，Azure Sentinel 在一小时内支持新密钥。

在 Key Vault 中，可以通过创建新的密钥版本来执行密钥轮换：

![密钥轮换](./media/customer-managed-keys/key-rotation.png)

你可以在24小时后禁用密钥的以前版本，或者在 Azure Key Vault 审核日志不再显示使用以前版本的任何活动后。

如果在 Azure Sentinel 和 Log Analytics 中使用相同的密钥，则必须执行密钥轮换，必须使用新的 Azure Key Vault 密钥版本来显式更新 Log Analytics 中的群集资源。 有关详细信息，请参阅[AZURE MONITOR CMK 旋转](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation)。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何在 Azure Sentinel 中设置客户管理的密钥。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。


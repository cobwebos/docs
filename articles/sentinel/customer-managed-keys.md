---
title: 在 Azure Sentinel 中设置客户托管密钥 | Microsoft Docs
description: 了解如何在 Azure Sentinel 中设置客户托管密钥 (CMK)。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/19/2020
ms.author: yelevin
ms.openlocfilehash: 58936066abcbe4c3f9fcfad78bf914c74079aa95
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141782"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>设置 Azure Sentinel 客户托管密钥


本文介绍了为 Azure Sentinel 配置客户托管密钥 (CMK) 的背景信息和步骤。 通过使用 CMK，可使用创建或拥有的 Azure Key Vault 密钥，在所有相关的存储资源中对所有保存或发送到 Azure Sentinel 的数据进行加密。

> [!NOTE]
> -   仅为新客户提供 Azure Sentinel CMK 功能，而且对该功能的访问由“Azure 功能注册”控制。 可通过联系 azuresentinelCMK@microsoft.com 请求访问权限，只要有可用容量，就会批准待批的请求。
> -   Azure Sentinel CMK 功能仅在美国东部、美国西部 2 和美国中部区域提供。
> -   CMK 功能仅提供给每天发送 1TB 及以上的客户。 当向 Microsoft 申请在 Azure 订阅上预配 CMK 时，会收到有关其他定价的信息。 了解有关 [Log Analytics](../azure-monitor/platform/manage-cost-storage.md#log-analytics-dedicated-clusters) 定价的详细信息。

## <a name="how-cmk-works"></a>CMK 的工作原理 

Azure Sentinel 解决方案将多个存储资源用于日志收集和功能，包括 Log Analytics 和其他功能。 作为 Azure Sentinel CMK 配置的一部分，还需要在相关存储资源上配置 CMK 设置。 在 Log Analytics 以外的存储资源中保存的数据也进行了加密。

详细了解 [CMK](../azure-monitor/platform/customer-managed-keys.md#customer-managed-key-cmk-overview)。

> [!NOTE]
> 如果在 Azure Sentinel 上启用了 CMK，则不会启用任何不支持 CMK 的公共预览功能。

## <a name="enable-cmk"></a>启用 CMK 

若要预配 CMK，请执行以下步骤： 

1.  创建 Azure Key Vault 和存储密钥。

2.  在 Log Analytics 工作区上启用 CMK。

3.  注册 Cosmos DB。

4.  将访问策略添加到 Azure Key Vault 实例。

5.  在 Azure Sentinel 中启用 CMK。

6.  启用 Azure Sentinel。

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>步骤 1：创建 Azure Key Vault 和存储密钥

1.  [创建 Azure Key Vault 资源](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910)，然后生成或导入用于数据加密的密钥。
    > [!NOTE]
    >  必须将 Azure Key Vault 配置为可恢复，以保护密钥和访问。

1.  [打开恢复选项：](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   请确保[软删除](../key-vault/general/soft-delete-overview.md)已打开。

    -   打开[清除保护](../key-vault/general/soft-delete-overview.md#purge-protection)可防止在软删除后强行删除机密/保管库。

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>步骤 2：在 Log Analytics 工作区上启用 CMK

按照 [Azure Monitor 客户托管的密钥配置](../azure-monitor/platform/customer-managed-keys.md)中的说明创建 CMK 工作区，以下步骤中将使用该工作区作为 Azure Sentinel 工作区。

### <a name="step-3-register-for-cosmos-db"></a>第 3 步：注册 Cosmos DB

Azure Sentinel 使用 Cosmos DB 作为附加存储资源。 请确保注册到 Cosmos DB。

按照 Cosmos DB 说明，为你的 Azure 订阅[注册 Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 资源提供程序。

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>第 4 步：将访问策略添加到 Azure Key Vault 实例

请确保添加 Cosmos DB 到 Azure Key Vault 实例的访问权限。 按照 Cosmos DB 说明，使用 Azure Cosmos DB 主体[将访问策略添加到 Azure Key Vault 实例](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)。

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>第 5 步：在 Azure Sentinel 中启用 CMK

仅当直接从 Azure 产品组接收访问权限后，才向新客户提供 Azure Sentinel CMK 功能。 使用 Microsoft 的联系人接收 Azure Sentinel 团队的批准，以便在解决方案中启用 CMK。

获得批准后，你需要提供以下信息来启用 CMK 功能。

-  要在其上启用 CMK 的工作区 ID

-  Key Vault URL：将密钥的 "密钥标识符" 复制到最后一个正斜杠：  
    

    ![密钥标识符](./media/customer-managed-keys/key-identifier.png)

    Azure Sentinel 团队会为你所提供的工作区启用 Azure Sentinel CMK 功能。

-  来自 Azure Sentinel 产品团队的验证，证明已批准你使用此功能。 必须先获得此验证，然后才能继续操作。

### <a name="step-6-enable-azure-sentinel"></a>第 6 步：启用 Azure Sentinel


请转到 Azure 门户，在要设置 CMK 的工作区中启用 Azure Sentinel。 有关详细信息，请参阅 [Azure Sentinel 加入](quickstart-onboard.md)。

## <a name="key-encryption-key-revocation-or-deletion"></a>密钥加密密钥吊销或删除


如果用户通过删除加密密钥或删除 Azure Sentinel 的访问权限来撤销密钥加密密钥，在一小时内，Azure Sentinel 将执行此更改，并表现为数据不再可用。 此时，将阻止执行任何使用持久性存储资源（如数据引入、持久配置更改和事件创建）的操作。 以前存储的数据不会删除，但仍无法访问。 不可访问的数据由数据保留策略管理，并根据该策略进行清除。

撤消或删除加密密钥后，唯一可行的操作是删除帐户。

如果在吊销后恢复访问操作，Azure Sentinel 会在一小时内恢复对数据的访问权限。

若要详细了解此功能在 Azure Monitor 中的工作原理，请参阅 [Azure Monitor CMK 吊销](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation)。

## <a name="key-encryption-key-rotation"></a>密钥加密密钥轮换


Azure Sentinel 和 Log Analytics 支持密钥轮换。 当用户在 Key Vault 执行密钥轮换时，Azure Sentinel 会在一小时内开始支持新密钥。

在 Key Vault 中，可通过创建新的密钥版本来执行密钥轮换：

![密钥轮换](./media/customer-managed-keys/key-rotation.png)

可以在 24 小时后，或者在 Azure Key Vault 审核日志不再显示任何使用以前版本的活动后，禁用密钥的以前版本。

如果在 Azure Sentinel 和 Log Analytics 中使用相同的密钥，则必须执行密钥轮换，必须使用新的 Azure Key Vault 密钥版本来显式更新 Log Analytics 中的群集资源。 有关详细信息，请参阅 [Azure Monitor CMK 轮换](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation)。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何在 Azure Sentinel 中设置客户托管密钥。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。


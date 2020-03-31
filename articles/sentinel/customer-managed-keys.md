---
title: 在 Azure Sentinel 中设置客户管理的密钥*微软文档
description: 了解如何在 Azure Sentinel 中设置客户管理密钥 （CMK）。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: yelevin
ms.openlocfilehash: bc2fa02925e7f5c671085eb87ca0431d3fca7691
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587951"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>设置 Azure 哨兵客户管理密钥


本文提供后台信息和步骤，用于为 Azure Sentinel 配置客户管理的密钥 （CMK）。 CMK 使保存或发送到 Azure Sentinel 的所有数据都可以使用您创建或拥有的 Azure 密钥保管库密钥在所有相关存储资源中加密。

> [!NOTE]
> -   Azure Sentinel CMK 功能仅提供给**新**客户，并且对此功能的访问由 Azure 功能注册控制。您可以通过联系azuresentinelCMK@microsoft.com请求访问，并且当容量可用时，挂起的请求将被批准。
> -   Azure 哨兵 CMK 功能仅在美国东部、美国西部 2 和中南部提供。
> -   CMK 功能仅适用于每天发送 1TB 或更多的客户。 当您向 Microsoft 申请在 Azure 订阅上预配 CMK 时，您将收到有关其他定价的信息。 了解有关[日志分析](../azure-monitor/platform/customer-managed-keys.md#disclaimers)充电的更多信息。

## <a name="how-cmk-works"></a>CMK 的工作原理 

Azure Sentinel 解决方案使用多个存储资源进行日志收集和功能，其中包括日志分析和其他存储资源。 作为 Azure Sentinel CMK 配置的一部分，您也将不得不在相关存储资源上配置 CMK 设置。 保存在日志分析以外的存储资源中的数据也将加密。

> [!NOTE]
> 如果在 Azure Sentinel 上启用 CMK，则不会启用不支持 CMK 的任何公共预览功能。

## <a name="enable-cmk"></a>启用 CMK 

要预配 CMK，请按照以下步骤操作： 

1.  创建 Azure 密钥保管库并存储密钥。

2.  在日志分析工作区上启用 CMK。

3.  注册宇宙数据库。

4.  向 Azure 密钥保管库实例添加访问策略。

5.  在 Azure 哨兵中启用 CMK。

6.  启用 Azure 哨兵。

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>第 1 步：创建 Azure 密钥保管库并存储密钥

1.  [创建 Azure 密钥保管库资源](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910)，然后生成或导入用于数据加密的密钥。
    > [!NOTE]
    >  Azure 密钥保管库必须配置为可恢复，以保护密钥和访问权限。

1.  [打开恢复选项：](../key-vault/key-vault-best-practices.md#turn-on-recovery-options)

    -   确保[软删除](../key-vault/key-vault-ovw-soft-delete.md)已打开。

    -   打开[清除保护](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)，以防止强制删除机密/保管库，即使在软删除后也是如此。

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>第 2 步：在日志分析工作区上启用 CMK

按照[Azure 监视器客户管理的关键配置](../azure-monitor/platform/customer-managed-keys.md)中的说明操作，以创建 CMK 工作区，在以下步骤中用作 Azure Sentinel 工作区。

### <a name="step-3-register-for-cosmos-db"></a>第 3 步：注册宇宙 DB

Azure Sentinel 与 Cosmos DB 一起用作其他存储资源。 请确保注册到宇宙数据库。

按照 Cosmos DB 指令注册 Azure 订阅[的 Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider)资源提供程序。

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>步骤 4：向 Azure 密钥保管库实例添加访问策略

请确保将从 Cosmos DB 到 Azure 密钥保管库实例的访问。 按照 Cosmos DB 说明向 Azure[密钥保管库实例添加具有](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)Azure 宇宙数据库主体的访问策略。

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>第 5 步：在 Azure 哨兵中启用 CMK

Azure Sentinel CMK 功能仅在直接从 Azure 产品组接收访问权限后提供给新客户。 使用 Microsoft 中的联系人获得 Azure Sentinel 团队的批准，以便在解决方案中启用 CMK。

获得批准后，系统将要求您提供以下信息以启用 CMK 功能。

-  要启用 CMK 的工作区 ID

-  密钥保管库 URL：将密钥的"密钥标识符"复制到最后一个前进斜杠：  
    

    ![密钥标识符](./media/customer-managed-keys/key-identifier.png)

    Azure 哨兵团队将为提供的工作区启用 Azure Sentinel CMK 功能。

-  Azure Sentinel 产品团队验证您已获准使用此功能。 在继续之前，您必须拥有此。

### <a name="step-6-enable-azure-sentinel"></a>第 6 步：启用 Azure 哨兵


转到 Azure 门户，并在设置 CMK 的工作区上启用 Azure Sentinel。 有关详细信息，请参阅[Azure 哨兵载入](quickstart-onboard.md)。

## <a name="key-encryption-key-revocation-or-deletion"></a>密钥加密密钥吊销或删除


如果用户通过删除密钥或删除 Azure Sentinel 的访问权限来撤消密钥加密密钥，Azure Sentinel 将在一小时内遵守更改，并像数据不再可用一样运行。 此时，将阻止使用持久存储资源（如数据引入、持久配置更改和事件创建）执行的任何操作。 以前存储的数据不会被删除，但仍无法访问。 无法访问的数据受数据保留策略的约束，并将根据该策略进行清除。

在吊销或删除加密密钥后，唯一可能的操作是帐户删除。

如果在吊销后恢复访问，Azure Sentinel 将在一小时内恢复对数据的访问。

要了解有关 Azure 监视器中工作原理的更多内容，请参阅[Azure 监视器 CMK 吊销](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation)。

## <a name="key-encryption-key-rotation"></a>密钥加密密钥轮换


Azure 哨兵和日志分析支持密钥轮换。 当用户在密钥保管库中执行密钥轮换时，Azure Sentinel 会在一小时内支持新密钥。

在密钥保管库中，您可以通过创建密钥的新版本来执行密钥轮换：

![键旋转](./media/customer-managed-keys/key-rotation.png)

您可以在 24 小时后或 Azure 密钥保管库审核日志不再显示使用早期版本的任何活动后禁用密钥的早期版本。

如果在 Azure Sentinel 和日志分析中使用同一密钥，则必须执行密钥轮换，则必须使用新的 Azure 密钥保管库密钥版本显式更新日志分析中的群集资源。 有关详细信息，请参阅[Azure 监视器 CMK 旋转](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation)。

## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何在 Azure Sentinel 中设置客户管理的密钥。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。


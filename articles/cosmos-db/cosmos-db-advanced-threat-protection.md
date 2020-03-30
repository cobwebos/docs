---
title: Azure 宇宙 DB 的高级威胁保护
description: 了解 Azure Cosmos DB 如何提供静态数据加密，以及如何实现数据。
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: bcc1c6ffe7cdec4aed325a67969235ae993a5109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614836"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Azure 宇宙 DB 的高级威胁保护（预览版）

Azure Cosmos DB 的高级威胁保护提供了一层额外的安全智能，用于检测访问或利用 Azure Cosmos DB 帐户的异常和潜在有害尝试。 此保护层允许您处理威胁，即使无需成为安全专家，也将其与中央安全监控系统集成。

当活动异常发生时，将触发安全警报。 这些安全警报与 Azure[安全中心](https://azure.microsoft.com/services/security-center/)集成，并且也通过电子邮件发送给订阅管理员，其中详细介绍了可疑活动，并就如何调查和修复威胁提出了建议。

> [!NOTE]
>
> * Azure Cosmos DB 的高级威胁保护当前仅适用于 SQL API。
> * Azure Cosmos DB 的高级威胁保护当前不在 Azure 政府和主权云区域中。

为了全面了解安全警报，我们建议[在 Azure Cosmos DB 中启用诊断日志记录](https://docs.microsoft.com/azure/cosmos-db/logging)，该 DB 会记录数据库本身的操作，包括对所有文档、容器和数据库的 CRUD 操作。

## <a name="threat-types"></a>威胁类型

Azure Cosmos DB 的高级威胁保护可检测异常活动，指示访问或利用数据库的异常和潜在有害尝试。 它当前可以触发以下警报：

- **从异常位置进行访问**：当 Azure Cosmos 帐户的访问模式发生更改时触发此警报，其中有人从异常地理位置连接到 Azure Cosmos DB 终结点。 在某些情况下，警报会检测到合法操作，这意味着新应用程序或开发人员的维护操作。 在其他情况下，警报会检测来自前员工、外部攻击者等的恶意操作。

- **异常数据提取**：当客户端从 Azure Cosmos DB 帐户中提取异常数量的数据时，将触发此警报。 这可能是为将帐户中存储的所有数据传输到外部数据存储而执行的某些数据外泄的症状。

## <a name="set-up-advanced-threat-protection"></a>设置高级威胁防护

### <a name="set-up-atp-using-the-portal"></a>使用门户设置 ATP

1. 在 启动 Azure[https://portal.azure.com](https://portal.azure.com/)门户。

2. 从 Azure Cosmos DB 帐户中，从 **"设置"** 菜单中选择 **"高级安全性**"。

    ![设置 ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. 在**高级安全**配置边栏选项卡中：

    * 单击 **"高级威胁防护"** 选项将其设置为**ON**。
    * 单击“保存”以保存新的或更新的高级威胁防护策略****。   

### <a name="set-up-atp-using-rest-api"></a>使用 REST API 设置 ATP

使用 Rest API 命令为特定的 Azure Cosmos DB 帐户创建、更新或获取高级威胁保护设置。

* [高级威胁防护 - 创建](https://go.microsoft.com/fwlink/?linkid=2099745)
* [高级威胁防护 - 获取](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>使用 Azure PowerShell 设置 ATP

使用以下 PowerShell cmdlet：

* [启用高级威胁防护](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [获得高级威胁防护](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [禁用高级威胁防护](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板

使用 Azure 资源管理器模板在启用了高级威胁保护后设置 Cosmos DB。
有关详细信息，请参阅[创建具有高级威胁保护的 CosmosDB 帐户](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)。

### <a name="using-azure-policy"></a>使用 Azure 策略

使用 Azure 策略为 Cosmos DB 启用高级威胁保护。

1. 启动 Azure**策略 - 定义**页，并搜索**部署 Cosmos DB 策略的高级威胁保护**。

    ![搜索策略](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. 单击"**部署 CosmosDB 策略的高级威胁保护**"，然后单击"**分配**"。

    ![选择订阅或组](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. 在 **"范围"** 字段中，单击三个点，选择 Azure 订阅或资源组，然后单击"**选择**"。

    ![策略定义页面](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. 输入其他参数，然后单击 **"分配**"。

## <a name="manage-atp-security-alerts"></a>管理 ATP 安全警报

当 Azure Cosmos DB 活动异常发生时，将触发安全警报，并包含有关可疑安全事件的信息。 

 从 Azure 安全中心，您可以查看和管理当前[安全警报](../security-center/security-center-alerts-overview.md)。  单击[安全中心](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0)中的特定警报，查看可能的原因和建议的操作，以调查和缓解潜在威胁。 下图显示了安全中心提供的警报详细信息示例。

 ![威胁详细信息](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

还会发送电子邮件通知，并包含警报详细信息和建议的操作。 下图显示了警报电子邮件的示例。

 ![警报详细信息](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>宇宙 DB ATP 警报

 要查看监视 Azure Cosmos DB 帐户时生成的警报列表，请参阅 Azure 安全中心文档中的[Cosmos DB 警报](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos)部分。

## <a name="next-steps"></a>后续步骤

* 了解有关[Azure 宇宙 DB 中的诊断日志记录](cosmosdb-monitor-resource-logs.md)的更多信息
* 了解有关[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)

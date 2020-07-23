---
title: 针对 Azure Cosmos DB 的高级威胁防护
description: 了解 Azure Cosmos DB 如何提供静态数据加密及其实现方式。
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: 0affd1660a88421f6df24bc5ef2e00497dae32a5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85119263"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>针对 Azure Cosmos DB 的高级威胁防护（预览版）

针对 Azure Cosmos DB 的高级威胁防护提供额外的一层安全智能，用于检测是否存在访问或利用 Azure Cosmos DB 帐户的异常且可能有害的企图。 无需成为安全专家，就能利用此保护层来应对威胁，并将威胁与中央安全监视系统集成。

当活动出现异常时，会触发安全警报。 这些安全警报与 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成，还通过电子邮件发送给订阅管理员，其中包含可疑活动的详细信息以及有关如何调查和修正威胁的建议。

> [!NOTE]
>
> * 针对 Azure Cosmos DB 的高级威胁防护目前仅适用于 SQL API。
> * 针对 Azure Cosmos DB 的高级威胁防护目前在 Azure 政府和主权云区域中不可用。

为了获取安全警报的完整调查体验，建议启用 [Azure Cosmos DB 中的诊断日志记录](https://docs.microsoft.com/azure/cosmos-db/logging)，它会记录对数据库本身执行的操作，包括对所有文档、容器和数据库执行的 CRUD 操作。

## <a name="threat-types"></a>威胁类型

针对 Azure Cosmos DB 的高级威胁防护会检测异常活动，这些活动指示存在访问或利用数据库的异常且可能有害的企图。 它可以触发以下警报：

- 来自异常位置的访问：当 Azure Cosmos 帐户的访问模式发生更改，有人从异常的地理位置连接到 Azure Cosmos DB 终结点时，会触发此警报。 在某些情况下，警报会检测合法操作，即新应用程序或开发人员的维护操作。 在其他情况下，警报会检测恶意操作（来自前员工、外部攻击者等）。

- 异常数据提取：当客户端从 Azure Cosmos DB 帐户提取的数据量异常时，会触发此警报。 这可能是为了将帐户中存储的所有数据传输到外部数据存储而执行的一些数据泄露的表现。



## <a name="configure-advanced-threat-protection"></a>配置高级威胁防护

可以通过多种方式来配置高级威胁防护，如以下部分中所述。

### <a name="portal"></a>[门户](#tab/azure-portal)

1. 在 [https://portal.azure.com](https://portal.azure.com/) 中启动 Azure 门户。

2. 从 Azure Cosmos DB 帐户的“设置”菜单中，选择“高级安全”。

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png" alt-text="设置 ATP":::

3. 在“高级安全”配置边栏选项卡中：

    * 单击“高级威胁防护”选项，将其设置为“打开”。
    * 单击“保存”以保存新的或更新的高级威胁防护策略。   

### <a name="rest-api"></a>[REST API](#tab/rest-api)

使用 Rest API 命令可创建、更新或获取特定 Azure Cosmos DB 帐户的高级威胁防护设置。

* [高级威胁防护 - 创建](https://go.microsoft.com/fwlink/?linkid=2099745)
* [高级威胁防护 - 获取](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用以下 PowerShell cmdlet：

* [启用高级威胁防护](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [获取高级威胁防护](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [禁用高级威胁防护](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="arm-template"></a>[ARM 模板](#tab/arm-template)

使用 Azure 资源管理器 (ARM) 模板可设置启用了高级威胁防护的 Cosmos DB。
有关详细信息，请参阅[创建具有高级威胁防护的 CosmosDB 帐户](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)。

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

使用 Azure Policy 可启用针对 Cosmos DB 的高级威胁防护。

1. 启动“Azure Policy - 定义”页面，然后搜索“部署针对 Cosmos DB 的高级威胁防护”策略。

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db.png" alt-text="搜索策略"::: 

1. 单击“部署针对 CosmosDB 的高级威胁防护”策略，然后单击“分配”。

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png" alt-text="选择订阅或组":::


1. 在“范围”字段中，单击三个点，选择 Azure 订阅或资源组，然后单击“选择”。

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png" alt-text="策略定义页":::


1. 输入其他参数，然后单击“分配”。




## <a name="manage-atp-security-alerts"></a>管理 ATP 安全警报

当 Azure Cosmos DB 活动出现异常时，会触发安全警报，其中包含有关可疑安全事件的信息。 

 可从 Azure 安全中心查看和管理当前[安全警报](../security-center/security-center-alerts-overview.md)。  单击[安全中心](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0)内的特定警报以查看可能的原因以及用于调查和缓解潜在威胁的建议操作。 下图显示了安全中心提供的警报详细信息的示例。

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png" alt-text="威胁详细信息":::

电子邮件通知还会随警报详细信息和建议的操作一起发送。 下图显示了警报电子邮件的示例。

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png" alt-text="警报详细信息":::

## <a name="cosmos-db-atp-alerts"></a>Cosmos DB ATP 警报

 若要查看在监视 Azure Cosmos DB 帐户时生成的警报的列表，请参阅 Azure 安全中心文档中的 [Cosmos DB 警报](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos)部分。

## <a name="next-steps"></a>后续步骤

* 了解有关 [Azure Cosmos DB 中的诊断日志记录](cosmosdb-monitor-resource-logs.md)的详细信息
* 了解有关 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)的详细信息

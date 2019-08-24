---
title: Azure Cosmos DB 的高级威胁防护
description: 了解 Azure Cosmos DB 如何提供静态数据的加密以及如何实现。
author: monhaber
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: v-mohabe
ms.custom: seodec18
ms.openlocfilehash: e22e05618c8b8977ba8b03afd3b654d27d4fa6bd
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981738"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db"></a>Azure Cosmos DB 的高级威胁防护

适用于 Azure Cosmos DB 的高级威胁防护提供额外的安全智能层, 用于检测访问或利用 Azure Cosmos DB 帐户的异常和潜在有害尝试。 利用这一层保护, 你可以解决威胁, 甚至无需成为安全专家, 还可以将它们与中心安全监视系统集成。

当发生异常时, 将触发安全警报。 这些安全警报与[Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成, 还会通过电子邮件发送给订阅管理员, 并详细介绍了可疑活动以及如何调查和修正威胁的建议。

> [!NOTE]
>
> * Azure Cosmos DB 的高级威胁防护目前仅适用于 SQL API。
> * Azure Cosmos DB 的高级威胁防护目前不适用于 Azure 政府版和主权的云区域。

为了获得安全警报的完整调查体验, 建议[在 Azure Cosmos DB 中启用诊断日志记录](https://docs.microsoft.com/azure/cosmos-db/logging), 这会在数据库本身上记录操作, 包括对所有文档、容器和数据库的 CRUD 操作。

## <a name="set-up-advanced-threat-protection"></a>设置高级威胁防护

### <a name="set-up-atp-using-the-portal"></a>使用门户设置 ATP

1. 启动 Azure 门户[https://portal.azure.com](https://portal.azure.com/)。

2. 从 Azure Cosmos DB 帐户的 "**设置**" 菜单中, 选择 "**高级安全**"。

    ![设置 ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. 在 "**高级安全**配置" 边栏选项卡中:

    * 单击 "**高级威胁防护**" 选项将其设置为 **"开**"。
    * 单击“保存”以保存新的或更新的高级威胁防护策略。   

### <a name="set-up-atp-using-rest-api"></a>使用 REST API 设置 ATP

使用 Rest API 命令创建、更新或获取特定 Azure Cosmos DB 帐户的高级威胁防护设置。

* [高级威胁防护-创建](https://go.microsoft.com/fwlink/?linkid=2099745)
* [高级威胁防护-获取](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>使用 Azure PowerShell 设置 ATP

使用以下 PowerShell cmdlet:

* [启用高级威胁防护](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [获取高级威胁防护](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [禁用高级威胁防护](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

## <a name="manage-atp-security-alerts"></a>管理 ATP 安全警报

发生 Azure Cosmos DB 活动异常情况时, 会触发安全警报, 其中包含有关可疑安全事件的信息。 

 通过 Azure 安全中心, 你可以查看和管理当前的[安全警报](../security-center/security-center-alerts-overview.md)。  单击 "[安全中心](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0)" 中的特定警报, 查看可能的原因和建议的操作, 以调查和缓解潜在威胁。 下图显示了在安全中心提供的警报详细信息的示例。

 ![威胁详细信息](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

还会随警报详细信息和建议的操作一起发送电子邮件通知。 下图显示了警报电子邮件的示例。

 ![警报详细信息](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Cosmos DB ATP 警报

 若要查看 Azure Cosmos DB 帐户监视时生成的警报的列表, 请参阅安全中心文档中的[Cosmos DB 警报](../security-center/security-center-alerts-data-services.md#cosmos-db)部分。

## <a name="next-steps"></a>后续步骤

* 详细了解[诊断日志记录 Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging#turn-on-logging-in-the-azure-portal)
* 了解有关 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)的详细信息

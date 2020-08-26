---
title: 迁移 Azure 服务总线命名空间 - 从标准到高级
description: 有关将现有 Azure 服务总线标准命名空间迁移到高级层的指南
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1ed09a077f086390c658e6650171c552b361008d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85340738"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>将现有 Azure 服务总线标准命名空间迁移到高级层

以前，Azure 服务总线仅在标准层上提供命名空间。 命名空间是针对低吞吐量和开发人员环境进行了优化的多租户设置。 高级层以固定的价格为每个命名空间提供专用资源，使延迟可预测，并提高了吞吐量。 高级层针对高吞吐量以及需要其他企业功能的生产环境进行了优化。

本文介绍了如何将现有的标准层命名空间迁移到高级层。  

>[!WARNING]
> 迁移适用于要升级到高级层的服务总线标准命名空间。 迁移工具不支持降级。

需要注意的一些要点：

- 此迁移将就地进行，这意味着，现有的发送方和接收方应用程序**不需要对代码或配置进行任何更改**。 现有的连接字符串将自动指向新的高级命名空间。
- 要使迁移成功，**高级**命名空间**不能包含任何实体**。
- 在迁移过程中，标准命名空间中的所有**实体**都将**复制**到高级命名空间。
- 在高级层上，迁移支持**每个消息传送单元包含 1,000 个实体**。 若要确定你需要多少个消息传送单元，请从当前标准命名空间上的实体数开始。
- 不能直接从**基本层**迁移到**高级层**，但可以间接地实现这种迁移：先从基本层迁移到标准层，然后从标准层迁移到高级层。

## <a name="migration-steps"></a>迁移步骤

迁移过程有一些相关条件。 请熟悉以下步骤以降低出错的可能性。 这些步骤概述了迁移过程，后面的部分将详细列出每个步骤。

1. 创建新的高级命名空间。
1. 将标准和高级命名空间相互配对。
1. 将标准命名空间中的实体同步（复制）到高级命名空间。
1. 提交迁移。
1. 使用命名空间在迁移后的名称清空标准命名空间中的实体。
1. 删除标准命名空间。

>[!IMPORTANT]
> 提交迁移后，访问旧的标准命名空间并清空队列和订阅。 清空消息后，可将其发送到新的高级命名空间，供接收方应用程序进行处理。 清空队列和订阅后，我们建议删除旧的标准命名空间。

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>使用 Azure CLI 或 PowerShell 进行迁移

若要使用 Azure CLI 或 PowerShell 工具将服务总线标准命名空间迁移到高级命名空间，请执行以下步骤。

1. 创建新的服务总线高级命名空间。 可以引用 [Azure 资源管理器模板](service-bus-resource-manager-namespace.md)或[使用 Azure 门户](service-bus-create-namespace-portal.md)。 对于 **serviceBusSku** 参数，请确保选择 **premium**。

1. 设置以下环境变量以简化迁移命令。

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > 迁移后，将使用迁移后的别名/名称 (post_migration_dns_name) 来访问旧的标准命名空间。 使用此别名/名称清空队列和订阅，然后删除命名空间。

1. 将标准和高级命名空间配对，并使用以下命令开始同步：

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. 使用以下命令检查迁移状态：

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    如果看到以下值，则认为迁移已完成：

    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Succeeded"

    此命令还会显示迁移配置。 检查并确保正确设置了值。 另外，请在门户中检查高级命名空间，确保已创建所有队列和主题，并且这些队列和主题与标准命名空间中的内容匹配。

1. 执行以下 complete 命令提交迁移：

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>使用 Azure 门户进行迁移

使用 Azure 门户进行迁移时的逻辑流与使用命令进行迁移时相同。 执行以下步骤以使用 Azure 门户进行迁移。

1. 在左侧窗格中的“导航”菜单中，选择“迁移到高级层”。   单击“开始”按钮转到下一页。 
    ![迁移登陆页][]

1. 完成“设置”。 
   ![设置命名空间][]
   1. 创建并分配要将现有标准命名空间迁移到的高级命名空间。
        ![设置命名空间 - 创建高级命名空间][]
   1. 选择“迁移后名称”。  迁移完成后，你将使用此名称来访问标准命名空间。
        ![设置命名空间 - 选择迁移后的名称][]
   1. 选择“下一步”继续操作。 
1. 在标准和高级命名空间之间同步实体。
    ![设置命名空间 - 同步实体 - 开始][]

   1. 选择“开始同步”以开始同步实体。 
   1. 在对话框中选择“是”，以确认并开始同步。 
   1. 等待同步完成。 状态栏上会显示状态。
        ![设置命名空间 - 同步实体 - 进度][]
        >[!IMPORTANT]
        > 如果出于任何原因需要中止迁移，请查看本文档的“常见问题解答”部分中的中止流。
   1. 完成同步后，选择页面底部的“下一步”。 

1. 在摘要页上查看更改。 选择“完成迁移”以切换命名空间并完成迁移。 
    ![切换命名空间 - 切换菜单][]  
    迁移完成后，会显示确认页。
    ![切换命名空间 - 成功][]

## <a name="caveats"></a>注意事项

Azure 服务总线高级层不支持 Azure 服务总线标准层提供的某些功能。 这是设计使然，因为高级层提供专用资源来使吞吐量和延迟可预测。

下面是高级层不支持的功能的列表以及相应的缓解措施 -

### <a name="express-entities"></a>快速实体

   高级层不支持不向存储提交任何消息数据的快速实体。 专用资源可以显著提高吞吐量，同时确保数据持久保存，这符合任何企业消息传送系统的预期。

   在迁移过程中，将在高级命名空间中以非快速实体的形式创建标准命名空间中的任何快速实体。

   如果你利用 Azure 资源管理器 (ARM) 模板，请确保从部署配置中删除“enableExpress”标志，使自动化工作流能够正常执行而不出错。

### <a name="partitioned-entities"></a>分区实体

   标准层支持分区实体，以便在多租户设置中提供更高的可用性。 在高级层中不再需要此功能，因为其中的每个命名空间都预配了专用资源。

   在迁移过程中，将在高级命名空间中以非分区实体的形式创建标准命名空间中的任何分区实体。

   如果 ARM 模板将特定队列或主题的“enablePartitioning”设置为“true”，则代理将忽略此队列或主题。

## <a name="faqs"></a>常见问题

### <a name="what-happens-when-the-migration-is-committed"></a>提交迁移后会发生什么情况？

提交迁移后，指向标准命名空间的连接字符串将指向高级命名空间。

发送方和接收方应用程序将断开到标准命名空间的连接，并自动重新连接到高级命名空间。

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>完成从标准到高级命名空间的迁移后需要执行哪些操作？

从标准到高级命名空间的迁移可确保将标准命名空间中的实体元数据（例如主题、订阅和筛选器）复制到高级命名空间。 已提交到标准命名空间的消息数据不会从标准命名空间复制到高级命名空间。

在迁移进行期间，可能有一些消息发送并提交到了标准命名空间。 请手动清空标准命名空间中的这些消息，并手动将其发送到高级命名空间。 若要手动清空消息，请通过某个控制台应用或脚本，使用在迁移命令中指定的迁移后 DNS 名称来清空标准命名空间实体。 将这些消息发送到高级命名空间，以便接收方能够对其进行处理。

清空消息后，删除标准命名空间。

>[!IMPORTANT]
> 清空标准命名空间中的消息后，删除标准命名空间。 此操作非常重要，因为最初引用标准命名空间的连接字符串现在引用高级命名空间。 不再需要标准命名空间。 删除已迁移的标准命名空间有助于避免今后发生混淆。

### <a name="how-much-downtime-do-i-expect"></a>停机时间预计有多长？

迁移过程会尽量减少应用程序的预计停机时间。 它将使用发送方和接收方应用程序用来指向新高级命名空间的连接字符串，从而减少停机时间。

应用程序经历的停机时间限制为更新 DNS 条目以指向高级命名空间所需的时间。 停机时间大约为 5 分钟。

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>执行迁移时是否需要更改任何配置？

执行迁移不需要更改任何代码或配置。 迁移过程会自动映射发送方和接收方应用程序用来访问标准命名空间的连接字符串，使其充当高级命名空间的别名。

### <a name="what-happens-when-i-abort-the-migration"></a>如果中止迁移，会发生什么情况？

可以使用 `Abort` 命令或 Azure 门户来中止迁移。

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure 门户

![中止流 - 中止同步][]
![中止流 - 中止完成][]

中止迁移过程时，会中止将标准命名空间中的实体（主题、订阅和筛选器）复制到高级命名空间的过程，并破坏配对。

连接字符串不会更新为指向高级命名空间。 现有应用程序像在开始迁移之前那样继续正常工作。

但是，这不会删除高级命名空间中的实体，也不会删除高级命名空间。 如果你决定不继续迁移，请手动删除这些实体。

>[!IMPORTANT]
> 如果你决定中止迁移，请删除为迁移预配的高级命名空间，以免产生资源费用。

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>我不想清空消息。 我该怎么办？

在迁移正在进行且即将提交迁移之前，发送方应用程序可能已发送一些消息并且这些消息已提交到标准命名空间中的存储。

在迁移过程中，实际的消息数据/有效负载不会从标准命名空间复制到高级命名空间。 必须手动清空消息，然后将其发送到高级命名空间。

但是，如果你可以在计划内维护/保养时段进行迁移，并且你不想手动清空并发送消息，请执行以下步骤：

1. 停止发送方应用程序。 接收方应用程序将处理当前位于标准命名空间中的消息，并清空队列。
1. 在标准命名空间中的队列和订阅已清空后，遵循前面所述的过程执行从标准到高级命名空间的迁移。
1. 迁移完成后，可以重启发送方应用程序。
1. 现在，发送方和接收方会自动连接到高级命名空间。

    >[!NOTE]
    > 无需停止接收方应用程序即可迁移。
    >
    > 迁移完成后，接收方应用程序将断开到标准命名空间的连接，并自动连接到高级命名空间。

## <a name="next-steps"></a>后续步骤

* 详细了解[标准和高级消息传送之间的差异](./service-bus-premium-messaging.md)。
* 了解[服务总线高级层的高可用性和异地灾难恢复](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)。

[迁移登陆页]: ./media/service-bus-standard-premium-migration/1.png
[设置命名空间]: ./media/service-bus-standard-premium-migration/2.png
[设置命名空间 - 创建高级命名空间]: ./media/service-bus-standard-premium-migration/3.png
[设置命名空间 - 选择迁移后名称]: ./media/service-bus-standard-premium-migration/4.png
[设置命名空间 - 同步实体 - 开始]: ./media/service-bus-standard-premium-migration/5.png
[设置命名空间 - 同步实体 - 进度]: ./media/service-bus-standard-premium-migration/8.png
[切换命名空间 - 切换菜单]: ./media/service-bus-standard-premium-migration/9.png
[切换命名空间 - 成功]: ./media/service-bus-standard-premium-migration/12.png

[中止流 - 中止同步]: ./media/service-bus-standard-premium-migration/abort1.png
[中止流 - 中止完成]: ./media/service-bus-standard-premium-migration/abort3.png

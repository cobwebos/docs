---
title: 将现有 Azure Service Bus 标准命名空间迁移到高级层 |Microsoft Docs
description: 允许将现有 Azure 服务总线标准命名空间迁移到高级版的指南
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: darosa
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 57ab281e8d07537c22bd3cf60306dfb1c7e81541
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "67566066"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>将现有 Azure Service Bus 标准命名空间迁移到高级层
以前, Azure 服务总线仅在标准层上提供了命名空间。 命名空间是多租户设置, 针对低吞吐量和开发人员环境进行了优化。 高级层为每个命名空间提供专用资源, 以实现可预测的延迟, 并以固定价格增加吞吐量。 高级层针对需要其他企业功能的高吞吐量和生产环境进行了优化。

本文介绍如何将现有标准层命名空间迁移到高级层。  

>[!WARNING]
> 迁移用于将服务总线标准命名空间升级到高级层。 迁移工具不支持降级。

需要注意的一些要点: 
- 此迁移旨在实现, 这意味着现有发送方和接收方应用程序**不需要对代码或配置进行任何更改**。 现有的连接字符串会自动指向新的高级命名空间。
- **高级**命名空间中**不应有任何实体**, 以便迁移成功。 
- 在迁移过程中, 标准命名空间中的所有**实体**都将**复制**到高级命名空间。 
- 在高级层上, 迁移支持**每个消息传送单元1000个实体**。 若要确定所需的消息传送单元数, 请从当前标准命名空间的实体数开始。 
- 你不能直接从**基本层**迁移到**高级层**, 但你可以通过从基本层迁移到标准层, 然后在下一步中从标准层迁移到高级层来间接执行此操作。

## <a name="migration-steps"></a>迁移步骤
某些条件与迁移过程相关联。 熟悉以下步骤以减少出现错误的可能性。 这些步骤概述了迁移过程, 后面的部分列出了分步详细说明。

1. 创建新的高级命名空间。
1. 将标准命名空间与高级命名空间配对。
1. 将实体从标准同步 (复制到高级命名空间)。
1. 提交迁移。
1. 使用命名空间的迁移后名称排出标准命名空间中的实体。
1. 删除标准命名空间。

>[!IMPORTANT]
> 提交迁移后, 访问旧的标准命名空间, 并清空队列和订阅。 消息排出后, 它们可能会发送到新的高级命名空间, 以供接收方应用程序处理。 队列和订阅完成后, 建议删除旧的标准命名空间。

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>使用 Azure CLI 或 PowerShell 进行迁移

若要使用 Azure CLI 或 PowerShell 工具将服务总线标准命名空间迁移到高级命名空间, 请执行以下步骤。

1. 创建新的服务总线高级命名空间。 可以参考[Azure 资源管理器模板](service-bus-resource-manager-namespace.md)或[使用 Azure 门户](service-bus-create-namespace-portal.md)。 请确保为**serviceBusSku**参数选择**高级**。

1. 设置以下环境变量以简化迁移命令。
   ```azurecli
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > 迁移后, 将使用迁移后别名/名称 (post_migration_dns_name) 访问旧的标准命名空间。 使用此来排出队列和订阅, 并删除命名空间。

1. 将标准命名空间与高级命名空间配对, 并使用以下命令开始同步:

    ```azurecli
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


1. 使用以下命令检查迁移状态:
    ```azurecli
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    当看到以下值时, 迁移将被视为已完成:
    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Succeeded"

    此命令还显示迁移配置。 检查以确保正确设置值。 另外, 请在门户中检查 "高级" 命名空间, 以确保已创建所有队列和主题, 并且这些队列和主题与标准命名空间中存在的内容匹配。

1. 通过执行以下 complete 命令提交迁移:
   ```azurecli
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>使用 Azure 门户迁移

使用 Azure 门户迁移与使用命令迁移的逻辑流相同。 按照以下步骤使用 Azure 门户进行迁移。

1. 在左侧窗格的**导航**菜单中, 选择 "**迁移到高级**"。 单击 "**开始**" 按钮转到下一页。
    ![迁移登陆页][]

1. 完成**安装**。
   ![安装命名空间][]
   1. 创建并分配高级命名空间以将现有标准命名空间迁移到。
        ![设置命名空间-创建高级命名空间][]
   1. 选择**迁移后的名称**。 迁移完成后, 你将使用此名称来访问标准命名空间。
        ![安装命名空间-选择迁移后名称][]
   1. 选择 **"下一步"** 以继续。
1. 在标准和高级命名空间之间同步实体。
    ![设置命名空间-同步实体-开始][]

   1. 选择 "**开始同步**" 以开始同步实体。
   1. 在对话框中选择 **"是"** 以确认并开始同步。
   1. 等待同步完成。 状态显示在状态栏上。
        ![设置命名空间-同步实体-进度][]
        >[!IMPORTANT]
        > 如果出于任何原因需要中止迁移, 请查看本文档的 "常见问题解答" 部分中的中止流程。
   1. 完成同步后, 在页面底部选择 "**下一步**"。

1. 查看 "摘要" 页上的更改。 选择 "**完成迁移**" 以切换命名空间并完成迁移。
    ![交换机命名空间-切换][]菜单迁移完成后, 将显示 "确认" 页。
    ![交换机命名空间-成功][]

## <a name="caveats"></a>注意事项

Azure 服务总线高级层不支持 Azure 服务总线标准层提供的某些功能。 这是由设计决定的, 因为高级层为可预测的吞吐量和延迟提供专用资源。

下面是高级版和缓解措施所不支持的功能的列表- 

### <a name="express-entities"></a>快速实体

   高级版不支持将任何消息数据提交到存储的快速实体。 专用资源提供了显著的吞吐量改进, 同时确保数据保持原样, 这与任何企业消息传递系统一样。
   
   在迁移过程中, 标准命名空间中的任何 express 实体都将在高级命名空间上作为非 express 实体创建。
   
   如果使用 Azure 资源管理器 (ARM) 模板, 请确保从部署配置中删除 "Microsoft.servicebus.messaging.queuedescription.enableexpress" 标志, 以便自动执行的工作流不会出错。

### <a name="partitioned-entities"></a>分区实体

   在标准层中支持已分区实体, 以在多租户设置中提供更好的可用性。 使用高级层中每个命名空间提供的专用资源, 就不再需要。
   
   在迁移期间, 标准命名空间中的任何分区实体都在高级命名空间中作为非分区实体创建。
   
   如果 ARM 模板将特定队列或主题的 "enablePartitioning" 设置为 "true", 则代理将忽略它。

## <a name="faqs"></a>常见问题

### <a name="what-happens-when-the-migration-is-committed"></a>提交迁移后会出现什么情况？

提交迁移后, 指向标准命名空间的连接字符串将指向高级命名空间。

发送方和接收方应用程序将与标准命名空间断开连接, 并自动重新连接到高级命名空间。

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>完成标准到高级迁移后, 我该做什么？

高级迁移标准确保将实体元数据 (例如主题、订阅和筛选器) 从标准命名空间复制到高级命名空间。 提交到标准命名空间的消息数据不会从标准命名空间复制到高级命名空间。

标准命名空间可能有一些消息, 这些消息在迁移过程中已发送和提交。 手动释放标准命名空间中的这些消息并将其手动发送到高级命名空间。 若要手动排出消息, 请使用控制台应用程序或脚本, 使用在迁移命令中指定的迁移后 DNS 名称来排出标准命名空间实体。 将这些消息发送到高级命名空间, 以便接收方可以处理这些消息。

消息排出后, 删除标准命名空间。

>[!IMPORTANT]
> 释放标准命名空间中的消息后, 请删除标准命名空间。 这一点很重要, 因为最初引用标准命名空间的连接字符串现在引用高级命名空间。 不再需要标准命名空间。 删除已迁移的标准命名空间有助于减少以后的混淆。

### <a name="how-much-downtime-do-i-expect"></a>我期望的停机时间是多少？
迁移过程旨在减少应用程序所需的停机时间。 通过使用发送方和接收方应用程序指向新的高级命名空间的连接字符串, 缩短停机时间。

应用程序所需的停机时间限制为指向高级命名空间所需的更新 DNS 条目所需的时间。 停机时间大约为5分钟。

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>是否需要在迁移过程中进行任何配置更改？
否, 不需要进行任何代码或配置更改即可执行迁移。 发送方和接收方应用程序用来访问标准命名空间的连接字符串会自动映射为充当高级命名空间的别名。

### <a name="what-happens-when-i-abort-the-migration"></a>中止迁移时会发生什么情况？
可以通过使用`Abort`命令或 Azure 门户来中止迁移。 

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure 门户

![中止流-中止同步][]
![中止流-中止完成][]

当迁移过程中止时, 它将中止从标准到高级命名空间将实体 (主题、订阅和筛选器) 复制到高级命名空间的过程, 并打破配对。

不会将连接字符串更新为指向高级命名空间。 在开始迁移之前, 你的现有应用程序将继续正常工作。

但是, 它不会删除高级命名空间上的实体, 也不会删除高级命名空间。 如果决定不继续迁移, 请手动删除这些实体。

>[!IMPORTANT]
> 如果决定中止迁移, 请删除为迁移预配的高级命名空间, 这样就不会对资源收费。

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>我不想要排出消息。 我该怎么办？

可能存在发送程序应用程序发送的消息, 并将其提交到标准命名空间上的存储, 同时进行迁移, 并在迁移迁移之前。

在迁移期间, 不会将实际的消息数据/有效负载从标准卷复制到高级命名空间。 必须手动排出消息, 然后将其发送到高级命名空间。

但是, 如果你可以在计划的维护/内务处理时段内迁移, 而不想手动排出并发送消息, 请执行以下步骤:

1. 停止发送方应用程序。 接收方应用程序将处理当前位于标准命名空间中的消息, 并将清空该队列。
1. 在标准命名空间中的队列和订阅为空后, 请按照前面所述的过程执行从标准到高级命名空间的迁移。
1. 迁移完成后, 可以重新启动发送方应用程序。
1. 发送方和接收方现在会自动连接到高级命名空间。

    >[!NOTE]
    > 对于迁移, 无需停止接收方应用程序。
    >
    > 迁移完成后, 接收方应用程序将与标准命名空间断开连接, 并自动连接到高级命名空间。

## <a name="next-steps"></a>后续步骤

* 了解有关[标准和高级消息传送之间的差异](./service-bus-premium-messaging.md)的详细信息。
* 了解[服务总线高级版的高可用性和异地灾难恢复方面](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)。

[迁移登陆页]: ./media/service-bus-standard-premium-migration/1.png
[安装命名空间]: ./media/service-bus-standard-premium-migration/2.png
[设置命名空间-创建高级命名空间]: ./media/service-bus-standard-premium-migration/3.png
[安装命名空间-选择迁移后名称]: ./media/service-bus-standard-premium-migration/4.png
[设置命名空间-同步实体-开始]: ./media/service-bus-standard-premium-migration/5.png
[设置命名空间-同步实体-进度]: ./media/service-bus-standard-premium-migration/8.png
[交换机命名空间-切换菜单]: ./media/service-bus-standard-premium-migration/9.png
[交换机命名空间-成功]: ./media/service-bus-standard-premium-migration/12.png

[中止流-中止同步]: ./media/service-bus-standard-premium-migration/abort1.png
[中止流-中止完成]: ./media/service-bus-standard-premium-migration/abort3.png

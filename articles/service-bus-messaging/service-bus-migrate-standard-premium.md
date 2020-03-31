---
title: 迁移 Azure 服务总线命名空间 - 标准到高级
description: 允许将现有 Azure 服务总线标准命名空间迁移到高级
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 27e3260b91bebee14ff12188a7dbd6c7cf76355c
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385021"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>将现有的 Azure 服务总线标准命名空间迁移到高级层

以前，Azure 服务总线仅在标准层上提供命名空间。 命名空间是针对低吞吐量和开发人员环境优化的多租户设置。 高级层为每个命名空间提供专用资源，以提供可预测的延迟和以固定价格提高吞吐量。 高级层针对需要其他企业功能的高吞吐量和生产环境进行了优化。

本文介绍如何将现有标准层命名空间迁移到高级层。  

>[!WARNING]
> 迁移用于将服务总线标准命名空间升级到高级层。 迁移工具不支持降级。

需要注意的一些要点：

- 此迁移旨在就地进行，这意味着现有的发送方和接收方应用程序**不需要对代码或配置进行任何更改**。 现有连接字符串将自动指向新的高级命名空间。
- **高级**命名空间中不应有**实体**，以便迁移成功。
- 在迁移过程中，标准命名空间中的所有**实体**都将**复制到**高级命名空间。
- 迁移支持高级层**上每个邮件单元 1，000 个实体**。 要确定所需的消息传递单元数，请从当前标准命名空间上的实体数开始。
- 不能直接从**基本层**迁移到**高级层**，但可以通过先从基本层迁移到标准，然后在下一步从标准迁移到高级层来间接迁移。

## <a name="migration-steps"></a>迁移步骤

某些条件与迁移过程相关联。 熟悉以下步骤，以减少出错的可能性。 这些步骤概述了迁移过程，后续详细信息将列在以下各节中。

1. 创建新的高级命名空间。
1. 将标准和高级命名空间彼此配对。
1. 将（复制）实体从标准同步到高级命名空间。
1. 提交迁移。
1. 通过使用命名空间的迁移后名称来排空标准命名空间中的实体。
1. 删除标准命名空间。

>[!IMPORTANT]
> 提交迁移后，访问旧标准命名空间并耗尽队列和订阅。 消息被排空后，它们可能会发送到新的高级命名空间，由接收方应用程序处理。 排空队列和订阅后，我们建议您删除旧的标准命名空间。

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>使用 Azure CLI 或 PowerShell 进行迁移

要使用 Azure CLI 或 PowerShell 工具将服务总线标准命名空间迁移到高级，请按照以下步骤操作。

1. 创建新的服务总线高级命名空间。 可以引用[Azure 资源管理器模板](service-bus-resource-manager-namespace.md)或使用[Azure 门户](service-bus-create-namespace-portal.md)。 请务必为**服务 BusSku**参数选择**高级**。

1. 设置以下环境变量以简化迁移命令。

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > 迁移后别名/名称（post_migration_dns_name）将用于访问迁移后的旧标准命名空间。 使用此选项可排空队列和订阅，然后删除命名空间。

1. 配对标准和高级命名空间，并使用以下命令启动同步：

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. 使用以下命令检查迁移的状态：

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    当您看到以下值时，迁移被视为已完成：

    * 迁移状态 = "活动"
    * 挂起复制操作计数 = 0
    * 预配状态 = "成功"

    此命令还显示迁移配置。 检查以确保正确设置值。 还要检查门户中的高级命名空间，以确保已创建所有队列和主题，并确保它们与标准命名空间中存在的内容匹配。

1. 通过执行以下完整命令提交迁移：

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>使用 Azure 门户进行迁移

使用 Azure 门户进行迁移与使用命令迁移具有相同的逻辑流。 按照以下步骤使用 Azure 门户进行迁移。

1. 在左侧窗格中的 **"导航"** 菜单上，选择 **"迁移到高级**"。 单击"**开始"** 按钮以继续下一页。
    ![迁移登录页][]

1. 完成**设置**。
   ![设置命名空间][]
   1. 创建并分配高级命名空间以将现有标准命名空间迁移到 。
        ![设置命名空间 - 创建高级命名空间][]
   1. 选择**迁移后名称**。 迁移完成后，您将使用此名称访问标准命名空间。
        ![设置命名空间 - 选取迁移后名称][]
   1. 选择 **"下一步"** 以继续。
1. 在标准命名空间和高级命名空间之间同步实体。
    ![设置命名空间 - 同步实体 - 启动][]

   1. 选择 **"开始同步"** 以开始同步实体。
   1. 在对话框中选择 **"是**"以确认并启动同步。
   1. 等待同步完成。 状态栏上提供状态。
        ![设置命名空间 - 同步实体 - 进度][]
        >[!IMPORTANT]
        > 如果您需要出于任何原因中止迁移，请查看本文档的常见问题部分中的中止流。
   1. 同步完成后，在页面底部选择 **"下一步**"。

1. 查看摘要页上的更改。 选择 **"完成迁移**"以切换命名空间并完成迁移。
    ![切换命名空间 - 切换菜单][]  
    迁移完成后，将显示确认页。
    ![切换命名空间 - 成功][]

## <a name="caveats"></a>注意事项

Azure 服务总线高级层不支持 Azure 服务总线标准层提供的某些功能。 这些是设计，因为高级层提供用于可预测的吞吐量和延迟的专用资源。

下面是高级版不支持的功能及其缓解措施的列表 -

### <a name="express-entities"></a>快速实体

   高级版不支持不向存储提交任何消息数据的快速实体。 专用资源提供了显著的吞吐量改进，同时确保数据持久化，正如任何企业邮件系统的预期的那样。

   在迁移期间，标准命名空间中的任何快速实体都将在高级命名空间上创建为非快递实体。

   如果使用 Azure 资源管理器 （ARM） 模板，请确保从部署配置中删除"enableExpress"标志，以便自动工作流执行时没有错误。

### <a name="partitioned-entities"></a>分区的实体

   标准层支持分区实体，以在多租户设置中提供更好的可用性。 由于高级层中每个命名空间提供专用资源，因此不再需要这样做。

   在迁移期间，标准命名空间中的任何分区实体都作为非分区实体在高级命名空间上创建。

   如果您的 ARM 模板将"启用分区"设置为特定队列或主题的"true"，则代理将忽略它。

## <a name="faqs"></a>常见问题解答

### <a name="what-happens-when-the-migration-is-committed"></a>提交迁移时会发生什么情况？

提交迁移后，指向标准命名空间的连接字符串将指向高级命名空间。

发送方和接收方应用程序将断开与标准命名空间的连接，并自动重新连接到高级命名空间。

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>标准到高级迁移完成后我该怎么办？

标准到高级迁移可确保实体元数据（如主题、订阅和筛选器）从标准命名空间复制到高级命名空间。 提交到标准命名空间的消息数据不会从标准命名空间复制到高级命名空间。

标准命名空间可能具有一些在迁移进行期间发送和提交的消息。 手动从标准命名空间排空这些消息，并手动将它们发送到高级命名空间。 要手动排空消息，请使用控制台应用或脚本，使用您在迁移命令中指定的迁移后 DNS 名称耗尽标准命名空间实体。 将这些消息发送到高级命名空间，以便接收方可以处理它们。

排空消息后，删除标准命名空间。

>[!IMPORTANT]
> 排空来自标准命名空间的消息后，删除标准命名空间。 这一点很重要，因为最初引用标准命名空间的连接字符串现在引用高级命名空间。 您不再需要标准命名空间了。 删除您迁移的标准命名空间有助于减少以后的混乱。

### <a name="how-much-downtime-do-i-expect"></a>我预计停机多少？

迁移过程旨在减少应用程序的预期停机时间。 通过使用发送方和接收方应用程序用来指向新的高级命名空间的连接字符串，减少了停机时间。

应用程序经历的停机时间仅限于更新 DNS 条目以指向高级命名空间所需的时间。 停机时间约为 5 分钟。

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>执行迁移时，我是否要进行任何配置更改？

否，无需执行迁移所需的代码或配置更改。 发送方和接收方应用程序用于访问标准命名空间的连接字符串将自动映射为高级命名空间的别名。

### <a name="what-happens-when-i-abort-the-migration"></a>中止迁移时会发生什么情况？

可以通过使用`Abort`命令或使用 Azure 门户中止迁移。

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure 门户

![中止流 -][]
![中止同步中止流 - 中止完成][]

中止迁移过程后，它将中止将实体（主题、订阅和筛选器）从标准复制到高级命名空间的过程，并中断配对。

连接字符串不会更新以指向高级命名空间。 现有应用程序将继续工作，就像开始迁移之前一样。

但是，它不会删除高级命名空间上的实体或删除高级命名空间。 如果您决定不继续迁移，请手动删除实体。

>[!IMPORTANT]
> 如果决定中止迁移，请删除已为迁移预配的高级命名空间，以便不向资源收费。

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>我不想把消息排干。 我该怎么办？

在迁移进行期间和提交迁移之前，可能有一些消息由发件人应用程序发送并提交到标准命名空间上的存储。

在迁移期间，实际的消息数据/有效负载不会从标准复制到高级命名空间。 必须手动排空消息，然后发送到高级命名空间。

但是，如果您在计划维护/内务管理时段内可以迁移，并且不希望手动排空和发送消息，请按照以下步骤操作：

1. 停止发件人应用程序。 接收方应用程序将处理当前位于标准命名空间中的消息，并将耗尽队列。
1. 在标准命名空间中的队列和订阅为空后，请按照前面描述的过程执行从标准到高级命名空间的迁移。
1. 迁移完成后，可以重新启动发件人应用程序。
1. 发送方和接收方现在将自动与高级命名空间连接。

    >[!NOTE]
    > 您不必停止迁移的接收方应用程序。
    >
    > 迁移完成后，接收器应用程序将断开与标准命名空间的连接，并自动连接到高级命名空间。

## <a name="next-steps"></a>后续步骤

* 详细了解[标准消息和高级消息之间的差异](./service-bus-premium-messaging.md)。
* 了解[服务总线高级的高可用性和地质灾害恢复方面](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)。

[迁移登录页]: ./media/service-bus-standard-premium-migration/1.png
[设置命名空间]: ./media/service-bus-standard-premium-migration/2.png
[设置命名空间 - 创建高级命名空间]: ./media/service-bus-standard-premium-migration/3.png
[设置命名空间 - 选取迁移后名称]: ./media/service-bus-standard-premium-migration/4.png
[设置命名空间 - 同步实体 - 启动]: ./media/service-bus-standard-premium-migration/5.png
[设置命名空间 - 同步实体 - 进度]: ./media/service-bus-standard-premium-migration/8.png
[切换命名空间 - 切换菜单]: ./media/service-bus-standard-premium-migration/9.png
[切换命名空间 - 成功]: ./media/service-bus-standard-premium-migration/12.png

[中止流 - 中止同步]: ./media/service-bus-standard-premium-migration/abort1.png
[中止流 - 中止完成]: ./media/service-bus-standard-premium-migration/abort3.png

---
title: 将现有的 Azure 服务总线标准命名空间迁移到高级层 |Microsoft Docs
description: 若要允许现有 Azure 服务总线标准命名空间的迁移到高级版指南
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
ms.date: 02/18/2019
ms.author: aschhab
ms.openlocfilehash: 7b153c36e10f1d4e2be2a0cf42f998c31cb6473a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896267"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-premium-tier"></a>将现有的 Azure 服务总线标准命名空间迁移到高级层

以前，Azure 服务总线提供仅在标准层命名空间。 这些是已针对低吞吐量和开发人员环境优化了的多租户设置。

最近，Azure 服务总线已扩展提供的高级层，它提供了每个可预测的延迟和更高的吞吐量，以固定价格进行了优化为较高的吞吐量和生产环境的命名空间的专用的资源需要额外的企业级功能。

以下工具启用现有的标准层命名空间迁移到高级层。

>[!WARNING]
> 迁移用于为服务总线标准级别命名空间***升级***到高级层。
>
> 迁移工具***却不***支持降级。
>[!NOTE]
> 此迁移旨在发生***就地***。
>
> 这意味着，现有的发送方和接收方应用程序不需要任何代码或配置更改。
>
> 现有的连接字符串会自动指向新的高级命名空间。
>
> 此外，对标准命名空间中的所有实体都都**复制**在迁移过程中的高级命名空间中。
>
>
> 我们支持***1000 个实体，每个消息传送单元***高级版，因此，若要确定多少消息传送单元需要请开始使用你在你当前的标准命名空间的实体数。

## <a name="migration-steps"></a>迁移步骤

>[!IMPORTANT]
> 有一些需要注意的问题与迁移过程相关联。 我们请求你完全了解了相关步骤便可减少错误的可能性。

以下指南详细介绍具体的步骤的迁移过程。

为所涉及的逻辑步骤-

1. 创建新的高级命名空间。
2. 对每个其他的标准和高级命名空间。
3. 从标准到高级命名空间的同步 （复制的） 实体
4. 提交迁移
5. 清空使用命名空间的迁移后名称对标准命名空间中的实体
6. 删除标准命名空间

>[!NOTE]
> 一旦迁移已提交，是非常重要，以访问旧的标准命名空间和清空队列和订阅。
>
> 消息将具有已断开后它们可能会发送到新的高级命名空间由接收方应用程序进行处理。
>
> 一旦队列和订阅具有将已断开，我们建议删除旧的标准命名空间。 您不需要使用它 ！

### <a name="migrate-using-azure-cli-or-powershell"></a>使用 Azure CLI 或 PowerShell 迁移

若要将你的服务总线标准级别命名空间迁移到高级版使用 Azure CLI 或 PowerShell 工具，请参阅以下指南。

1. 创建新的服务总线高级命名空间。 可以引用[Azure 资源管理器模板](service-bus-resource-manager-namespace.md)或[使用 Azure 门户](service-bus-create-namespace-portal.md)。 请务必选择"高级" **serviceBusSku**参数。

2. 设置以下环境变量，从而简化迁移命令。
   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the Premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the Standard namespace>
   ```

    >[!IMPORTANT]
    > 迁移后名称 (post_migration_dns_name) 将用于访问旧迁移的标准命名空间后。 必须使用此清空队列和订阅，然后删除该命名空间。

3. **对**标准和高级命名空间和**开始同步**使用以下命令-

    ```
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


4. 检查状态迁移使用以下命令-
    ```
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    迁移被视为完成时
    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Succeeded"

    此命令还显示迁移配置。 请仔细检查以确保将值设置为上一个声明。

    此外，还检查以确保已创建所有队列和主题，并且它们与匹配内容存在于标准命名空间上在门户中的高级命名空间。

5. 通过执行下面的完整命令提交迁移
   ```
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-using-azure-portal"></a>使用 Azure 门户迁移

通过 Azure 门户的迁移都是为迁移使用的命令相同的逻辑流。 请参阅下面的分步过程的指南，使用门户进行迁移。

1. 选取**迁移到高级版**从导航菜单上的左窗格中的菜单选项。 单击**开始**按钮可前进到下一步的页。
    ![迁移登录页面][]

2. 完整**安装程序**。
   ![安装程序命名空间][]
   1. 创建并分配要迁移到现有的标准命名空间的高级命名空间。
        ![设置命名空间-创建高级命名空间][]
   2. 选取**迁移后 name**完成迁移后访问通过标准的命名空间。
        ![设置命名空间-选取 post 迁移名称][]
   3. 单击**下一步**以继续。
3. **同步**实体之间的标准和高级命名空间。
    ![设置命名空间的同步实体-开始][]

   1. 单击**开始同步**开始同步实体。
   2. 单击**是**上弹出窗口中确认要启动同步。
   3. 等待，直到**同步**已完成。 在状态栏上，将提供状态。
        ![设置命名空间的同步实体-进度][]
        >[!IMPORTANT]
        > 如果你需要**中止**出于任何原因，请查看本文档的常见问题部分中的中止流。
   4. 在同步完成后，单击**下一步**在页面底部的按钮。

4. 查看摘要页上的更改。
    ![命名空间的切换菜单切换][]

5. 单击**完成迁移**切换命名空间并完成迁移。
    ![开关命名空间-成功][]

## <a name="faqs"></a>常见问题解答

### <a name="what-happens-when-the-migration-is-committed"></a>提交迁移时，会发生什么情况？

提交迁移后，指向标准命名空间的连接字符串将指向高级命名空间。

发送方和接收方应用程序将从标准 Namespace 断开连接，并自动重新连接到高级命名空间。

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>执行哪些操作的标准到高级版的迁移完成后，我？

标准到高级迁移确保实体元数据 （主题、 订阅、 筛选器，等等） 会复制对从标准到高级命名空间。 已提交到标准命名空间的消息数据是不会复制从标准到高级命名空间。

因此，对标准命名空间可能已发送和提交迁移时的一些消息。 这些消息必须手动从标准 Namespace 排出并发送至高级 Namespace 手动。

为此，你***必须***使用控制台应用程序或脚本清空使用标准命名空间实体**Post 迁移 DNS 名称**迁移命令中指定，然后发送这些消息上高级 Namespace，以便它们可以接收方处理。

之后消息将具有已断开，请继续进行删除标准命名空间。

>[!IMPORTANT]
> 请注意，对标准命名空间中的消息将具有已断开，一旦您**必须**删除标准命名空间。
>
> 这非常重要，因为最初现在称为标准命名空间的连接字符串实际上引用的高级命名空间。 您不需要使用此标准 Namespace 不再。
>
> 正在删除迁移可帮助对标准命名空间可以减少在以后的混淆。 

### <a name="how-much-downtime-do-i-expect"></a>我预计停机时间？
上面所述的迁移过程旨在减少应用程序的预期停机时间。 这是通过使用发送方和接收方应用程序用来指向新的高级命名空间的连接字符串。

经验丰富的应用程序的停机时间仅限于以更新 DNS 条目以指向高级命名空间所需的时间量。

这可以假定要***大约为 5 分钟***。

### <a name="do-i-have-to-make-any-configuration-changes-while-performing-the-migration"></a>我是否需要执行迁移时进行任何配置更改？
否，没有执行此迁移所需的代码/配置更改。 发送方和接收方应用程序用于访问标准 Namespace 的连接字符串进行自动映射，以充当**别名**的高级 Namespace。

### <a name="what-happens-when-i-abort-the-migration"></a>当我中止迁移时，会发生什么情况？
通过使用中止命令或通过 Azure 门户，可以中止迁移。 

#### <a name="azure-cli-or-powershell"></a>Azure CLI 或 PowerShell

    az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace

#### <a name="azure-portal"></a>Azure 门户

![中止流-中止同步][]
![中止流-中止完成][]

当中止迁移过程时，它实际上中止的实体 （主题、 订阅和筛选器） 通过复制过程从标准到高级命名空间，中断配对。

连接字符串**不是**更新为指向高级命名空间。 将现有应用程序继续工作就像你在开始迁移前。

但是，它**却不**删除高级命名空间上的实体，或者删除高级命名空间本身。 这必须如果您只是决定不继续进行迁移毕竟手动完成。

>[!IMPORTANT]
> 如果您决定中止迁移，请删除已迁移时，配高级 Namespace，以便您无需支付资源。

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>我不想必须清空这些消息。 我该怎么办？

可能是由发件人应用程序发送和迁移正在进行，而提交到标准 Namespace 上存储的消息，在迁移之前的权限是已提交。

考虑到在迁移期间，实际的消息数据/负载不会复制从标准到高级版，它们必须手动清空，然后发送到高级命名空间。

但是，如果在计划内的维护/维护时段内可以迁移并不想要手动清空和发送消息，请按以下步骤-

1. 停止发件人应用程序，并允许接收方处理的消息的当前正在对标准命名空间和清空队列。
2. 队列和标准 Namespace 中的订阅都为空，按照上述从标准到高级命名空间执行迁移过程。
3. 在迁移完成后，可以重新启动的发件人应用程序。
4. 发送者和接收者会立即自动连接与高级命名空间。

    >[!NOTE]
    > 为迁移，需要停止接收方。
    >
    > 在迁移完成后，接收方将断开与对标准命名空间的连接，并自动连接到高级命名空间。

## <a name="next-steps"></a>后续步骤

* 详细了解[标准和高级消息传送之间的差异](./service-bus-premium-messaging.md)
* 了解用于服务总线高级版的高可用性和异地灾难恢复方面[此处](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)

[迁移登录页面]: ./media/service-bus-standard-premium-migration/1.png
[安装程序命名空间]: ./media/service-bus-standard-premium-migration/2.png
[设置命名空间-创建高级命名空间]: ./media/service-bus-standard-premium-migration/3.png
[设置命名空间-选取 post 迁移名称]: ./media/service-bus-standard-premium-migration/4.png
[设置命名空间的同步实体-开始]: ./media/service-bus-standard-premium-migration/5.png
[设置命名空间的同步实体-进度]: ./media/service-bus-standard-premium-migration/8.png
[命名空间的切换菜单切换]: ./media/service-bus-standard-premium-migration/9.png
[开关命名空间-成功]: ./media/service-bus-standard-premium-migration/12.png

[中止流-中止同步]: ./media/service-bus-standard-premium-migration/abort1.png
[中止流-中止完成]: ./media/service-bus-standard-premium-migration/abort3.png

---
title: 将现有 Azure 服务总线标准命名空间迁移到高级层 |Microsoft Docs
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
ms.openlocfilehash: d2aa018566695b5b94cd8a7e64931a8b776b151d
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766294"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>将现有 Azure 服务总线标准命名空间迁移到高级层
以前，Azure 服务总线提供仅在标准层命名空间。 命名空间是适用于低吞吐量和开发人员环境的多租户设置。 高级级别提供的每个可预测的延迟和更高的吞吐量，以固定价格的命名空间的专用的资源。 高级层适用于高吞吐量和生产环境需要额外的企业级功能。

本文介绍如何将现有的标准层命名空间迁移到高级层。

>[!WARNING]
> 迁移适用于服务总线标准命名空间要升级到高级层。 迁移工具不支持降级。

一些需要注意的要点： 
- 此迁移应发生的位置，这意味着现有的发送方和接收方应用程序**不需要对代码或配置的任何更改**。 现有的连接字符串会自动指向新的高级命名空间。
- **Premium**命名空间应具有**没有实体**中它为使迁移取得成功。 
- 所有**实体**标准命名空间中都**复制**到在迁移过程中的高级命名空间。 
- 迁移支持**1,000 个实体，每个消息传送单元**在高级层。 若要确定需要多少消息传送单元，开始对您当前的标准命名空间的实体数。 

## <a name="migration-steps"></a>迁移步骤
某些条件是迁移过程与相关联。 熟悉以下步骤以降低错误的可能性。 以下步骤概述了迁移的过程，并在后面的部分中列出的分步详细信息。

1. 创建新的高级命名空间。
1. 对每个其他的标准和高级命名空间。
1. 同步 （复制的） 实体从标准到高级命名空间。
1. 提交迁移。
1. 通过使用迁移后的名称的命名空间的耗尽标准命名空间中的实体。
1. 删除标准命名空间。

>[!IMPORTANT]
> 迁移已提交后，访问旧的标准命名空间，并清空队列和订阅。 消息已耗尽后，它们可能会发送到新的高级命名空间由接收方应用程序进行处理。 已清空队列和订阅后，我们建议您删除旧的标准命名空间。

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>使用 Azure CLI 或 PowerShell 迁移

若要使用 Azure CLI 或 PowerShell 工具将你的服务总线标准命名空间迁移到高级版，请执行以下步骤。

1. 创建新的服务总线高级命名空间。 可以引用[Azure 资源管理器模板](service-bus-resource-manager-namespace.md)或[使用 Azure 门户](service-bus-create-namespace-portal.md)。 请务必选择**premium**有关**serviceBusSku**参数。

1. 设置以下环境变量以简化迁移命令。
   ```azurecli
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > 迁移后名称 (post_migration_dns_name) 将用于访问旧迁移的标准命名空间后。 使用此选项可清空队列和订阅，然后再删除该命名空间。

1. 对标准和高级命名空间和启动同步。 使用以下命令：

    ```azurecli
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


1. 使用以下命令检查迁移的状态：
    ```azurecli
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    当看到以下值时，被视为完成迁移：
    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Succeeded"

    此命令还显示迁移配置。 检查以确保正确设置值。 此外检查以确保已创建所有队列和主题，并且它们与匹配内容存在于标准命名空间中在门户中的高级命名空间。

1. 通过执行以下完整命令提交迁移：
   ```azurecli
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>使用 Azure 门户迁移

使用 Azure 门户的迁移都是相同的逻辑流作为迁移使用命令。 请按照以下步骤来使用 Azure 门户迁移。

1. 上**导航**的左窗格中，选择菜单**迁移到高级**。 单击**开始**按钮以继续进入到下一页。
    ![迁移登录页面][]

1. 完整**安装程序**。
   ![安装程序命名空间][]
   1. 创建并分配要迁移到现有的标准命名空间的高级命名空间。
        ![设置命名空间-创建高级命名空间][]
   1. 选择**Post 迁移名称**。 此名称将用于完成迁移后访问的标准命名空间。
        ![设置命名空间-选取 post 迁移名称][]
   1. 选择**下一步**以继续。
1. 标准和高级命名空间之间的同步实体。
    ![设置命名空间的同步实体-开始][]

   1. 选择**开始同步**开始同步实体。
   1. 选择**是**在对话框中确认并开始同步。
   1. 等待，直到同步已完成。 状态为状态栏上可用。
        ![设置命名空间的同步实体-进度][]
        >[!IMPORTANT]
        > 如果你需要出于任何原因中止迁移，请查看本文档的常见问题部分中的中止流。
   1. 在同步完成后，选择**下一步**页的底部。

1. 查看摘要页上的更改。 选择**完成迁移**要切换的命名空间，以完成迁移。
    ![命名空间的切换菜单切换][]迁移完成时，会显示确认页。
    ![开关命名空间-成功][]

## <a name="faqs"></a>常见问题解答

### <a name="what-happens-when-the-migration-is-committed"></a>提交迁移时，会发生什么情况？

提交迁移后，指向标准命名空间的连接字符串将指向高级命名空间。

发送方和接收方应用程序将从标准 Namespace 断开连接，并自动重新连接到高级命名空间。

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>执行哪些操作的标准到高级版的迁移完成后，我？

标准到高级迁移确保实体元数据，如主题、 订阅和筛选器会从标准命名空间复制到高级命名空间。 已提交到标准命名空间的消息数据不是从对标准命名空间复制到高级命名空间。

对标准命名空间可能已发送和提交迁移时的一些消息。 手动完从标准 Namespace 这些消息，然后手动将其发送到高级 Namespace。 若要手动释放消息，请使用一个控制台应用程序或通过使用迁移命令中指定的 Post 迁移 DNS 名称耗尽标准命名空间实体的脚本。 将这些消息发送到高级命名空间，以便它们可以由接收方处理。

消息已耗尽后，删除对标准命名空间。

>[!IMPORTANT]
> 标准命名空间中的消息已耗尽后，删除对标准命名空间。 这非常重要，因为最初现在称为标准命名空间的连接字符串是指高级命名空间。 不会不再需要标准 Namespace。 删除已迁移的标准命名空间可帮助减少更高版本的混淆。

### <a name="how-much-downtime-do-i-expect"></a>我预计停机时间？
迁移过程旨在减少应用程序的预期停机时间。 通过使用发送方和接收方应用程序用来指向新的高级命名空间的连接字符串是减少了停机时间。

应用程序所经历的停机时间仅限于所需更新 DNS 条目以指向高级命名空间的时间。 停机时间为大约 5 分钟。

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>我是否需要执行迁移时进行任何配置更改？
否，没有执行迁移所需的代码或配置更改。 发送方和接收方应用程序用于访问标准 Namespace 的连接字符串进行自动映射，以充当高级命名空间的别名。

### <a name="what-happens-when-i-abort-the-migration"></a>当我中止迁移时，会发生什么情况？
迁移可以中止使用`Abort`命令或通过使用 Azure 门户。 

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure 门户

![中止流-中止同步][]
![中止流-中止完成][]

在中止迁移过程，它会中止复制实体 （主题、 订阅和筛选器） 从标准到高级命名空间的过程并中断配对。

连接字符串不会更新为指向高级命名空间。 将现有应用程序继续工作就像你在开始迁移前。

但是，它不会删除高级命名空间上的实体，或者删除高级命名空间。 如果你决定不继续进行迁移，请手动删除这些实体。

>[!IMPORTANT]
> 如果您决定中止迁移，删除高级，以便您无需支付资源已迁移配 Namespace。

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>我不想必须清空这些消息。 我该怎么办？

可能是由发件人应用程序发送和迁移正在进行时和迁移提交之前只需提交到标准 Namespace 上存储的消息。

在迁移期间，实际的消息数据/负载不被复制标准到高级命名空间。 消息必须手动清空，然后发送到高级命名空间。

但是，如果在计划内的维护/维护时段，可以迁移，并且不想要手动清空和发送消息，请按照下列步骤：

1. 停止发件人应用程序。 接收方应用程序将处理的消息的当前处于标准命名空间中，将清空队列。
1. 空的队列和标准 Namespace 中的订阅后，请按照前面所述从标准到高级命名空间执行迁移的过程。
1. 在迁移完毕后，可以重新启动的发件人应用程序。
1. 发送者和接收者会立即自动连接与高级命名空间。

    >[!NOTE]
    > 无需停止迁移的接收方应用程序。
    >
    > 在迁移完毕后，接收方应用程序将断开与对标准命名空间的连接，并自动连接到高级命名空间。

## <a name="next-steps"></a>后续步骤

* 详细了解如何[标准和高级消息传送之间的差异](./service-bus-premium-messaging.md)。
* 了解如何[服务总线高级版的高可用性和异地灾难恢复方面](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)。

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

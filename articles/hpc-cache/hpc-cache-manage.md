---
title: 管理和更新 Azure HPC 缓存
description: 如何使用 Azure 门户管理和更新 Azure HPC 缓存
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: da260074fc69fac9e98d3698bb2d40fdf80d7118
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252036"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>从 Azure 门户管理缓存

Azure 门户中的 "缓存概述" 页显示缓存的项目详细信息、缓存状态和基本统计信息。 它还包含用于停止或启动缓存、删除缓存、将数据刷新到长期存储和更新软件的控件。

若要打开 "概述" 页，请在 Azure 门户中选择缓存资源。 例如，加载 "**所有资源**" 页，然后单击缓存名称。

![Azure HPC 缓存实例的 "概述" 页的屏幕截图](media/hpc-cache-overview.png)

页面顶部的按钮可帮助你管理缓存：

* **启动**和[**停止**](#stop-the-cache)-暂停缓存操作
* [**刷新**](#flush-cached-data)-将更改的数据写入存储目标
* [**升级**](#upgrade-cache-software)-更新缓存软件
* **刷新**-重新加载 "概述" 页
* [**删除**](#delete-the-cache)-永久销毁缓存

阅读以下有关这些选项的详细信息。

## <a name="stop-the-cache"></a>停止缓存

可以停止缓存，降低非活动期间的成本。 停止缓存后，不会向你收费，但会向你收取缓存分配的磁盘存储费用。 （有关详细信息，请参阅[定价](https://aka.ms/hpc-cache-pricing)页。）

停止的缓存不响应客户端请求。 应在停止缓存之前卸载客户端。

"**停止**" 按钮可挂起活动缓存。 当缓存的状态为 "**正常**" 或 "已**降级**" 时，"**停止**" 按钮可用。

![已突出显示 "停止" 操作并显示一条弹出消息，其中显示 "停止" 操作并询问 "是否要继续？" 的顶部按钮的屏幕截图 具有 "是" （默认值）和 "无" 按钮](media/stop-cache.png)

单击 "是" 确认停止缓存后，缓存会自动将其内容刷新到存储目标。 此过程可能需要一段时间，但它可以确保数据的一致性。 最后，缓存状态将更改为 "**已停止**"。

若要重新激活停止的缓存，请单击 "**开始**" 按钮。 不需要确认。

![突出显示启动的顶部按钮的屏幕截图](media/start-cache.png)

## <a name="flush-cached-data"></a>刷新缓存的数据

"概述" 页上的 "**刷新**" 按钮通知缓存将存储在缓存中的所有已更改数据立即写入后端存储目标。 缓存会定期将数据保存到存储目标，因此，无需手动执行此操作，除非你想要确保后端存储系统是最新的。 例如，可以在拍摄存储快照之前使用**Flush** ，或检查数据集的大小。

> [!NOTE]
> 在刷新过程中，缓存无法为客户端请求提供服务。 在操作完成后，缓存访问被挂起并恢复。

![突出显示了突出显示的顶部按钮的屏幕截图和一个弹出消息，其中描述了刷新操作并询问 "是否继续？" 具有 "是" （默认值）和 "无" 按钮](media/hpc-cache-flush.png)

当你开始缓存刷新操作时，缓存将停止接受客户端请求，"概述" 页上的缓存状态将更改为 "正在**刷新**"。

缓存中的数据将保存到相应的存储目标。 根据需要刷新的数据量，此过程可能需要几分钟或几小时。

将所有数据保存到存储目标后，该缓存将自动开始重新获取客户端请求。 缓存状态返回为 "**正常**"。

## <a name="upgrade-cache-software"></a>升级缓存软件

如果有新的软件版本，则 "**升级**" 按钮将变为活动状态。 你还会在有关更新软件的页面顶部看到一条消息。

![启用了 "升级" 按钮的按钮顶行屏幕截图](media/hpc-cache-upgrade-button.png)

在软件升级过程中，客户端访问不会中断，但缓存性能会降低。 计划在非高峰使用时间或计划内维护期间升级软件。

软件更新可能需要几个小时。 比具有较小的高峰吞吐量值的缓存相比，使用更高吞吐量配置的缓存需要更长的时间来升级

当软件升级可用时，你会有一周的时间来手动应用它。 结束日期将在升级消息中列出。 如果在这段时间内没有升级，Azure 会自动将更新应用于缓存。 自动升级的时间不能配置。 如果你担心缓存性能影响，你应在此时间段到期之前自行升级软件。

如果在结束日期通过时缓存停止，缓存将在下一次启动时自动升级软件。 （更新可能不会立即启动，但会在第一小时开始。）

单击 "**升级**" 按钮以开始软件更新。 在操作完成之前，缓存状态将更改为 "正在**升级**"。

## <a name="delete-the-cache"></a>删除缓存

"**删除**" 按钮会销毁缓存。 删除缓存时，它的所有资源都将被销毁，不再产生帐户费用。

当你删除缓存时，用作存储目标的后端存储卷不会受到影响。 稍后可将其添加到将来的缓存，或单独解除其授权。

> [!NOTE]
> 在删除缓存之前，Azure HPC 缓存不会自动将已更改的数据从缓存写入后端存储系统。
>
> 若要确保缓存中的所有数据都已写入到长期存储，请在删除之前[停止缓存](#stop-the-cache)。 在单击 "删除" 按钮之前，请确保它显示 "**已停止**" 状态。
<!--... written to long-term storage, follow this procedure:
>
> 1. [Remove](hpc-cache-edit-storage.md#remove-a-storage-target) each storage target from the Azure HPC Cache by using the delete button on the Storage targets page. The system automatically writes any changed data from the cache to the back-end storage system before removing the target.
> 1. Wait for the storage target to be completely removed. The process can take an hour or longer if there is a lot of data to write from the cache. When it is done, a portal notification says that the delete operation was successful, and the storage target disappears from the list.
> 1. After all affected storage targets have been deleted, it is safe to delete the cache.
>
> Alternatively, you can use the [flush](#flush-cached-data) option to save cached data, but there is a small risk of losing work if a client writes a change to the cache after the flush completes but before the cache instance is destroyed.-->

## <a name="cache-metrics-and-monitoring"></a>缓存指标和监视

"概述" 页显示一些基本缓存统计信息的图形-缓存吞吐量、每秒操作数和延迟时间。

![显示上述示例缓存的统计信息的三个折线图的屏幕截图](media/hpc-cache-overview-stats.png)

这些图表是 Azure 内置监视和分析工具的一部分。 可以从门户侧栏中 "**监视**" 标题下的页面获得其他工具和警报。 有关详细信息，请[参阅 Azure 监视文档](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal)的门户部分。

## <a name="next-steps"></a>后续步骤

<!-- * Learn more about metrics and statistics for hpc cache -->
* 了解有关[Azure 指标和统计信息工具的](../azure-monitor/index.yml)详细信息
* 获取[有关 AZURE HPC 缓存的帮助](hpc-cache-support-ticket.md)

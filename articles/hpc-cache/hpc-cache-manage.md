---
title: 管理和更新 Azure HPC 缓存
description: 如何使用 Azure 门户管理和更新 Azure HPC 缓存
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: a166a904b2e63419efd5803fd54be1d1b59836fb
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867084"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>从 Azure 门户管理缓存

Azure 门户中的 "缓存概述" 页显示缓存的项目详细信息、缓存状态和基本统计信息。 它还包含用于删除缓存、将数据刷新到长期存储或更新软件的控件。

若要打开 "概述" 页，请在 Azure 门户中选择缓存资源。 例如，加载 "**所有资源**" 页，然后单击缓存名称。

![Azure HPC 缓存实例的 "概述" 页的屏幕截图](media/hpc-cache-overview.png)

页面顶部的按钮可帮助你管理缓存：

* [**刷新**](#flush-cached-data)-将更改的数据写入存储目标
* [**升级**](#upgrade-cache-software)-更新缓存软件
* **刷新**-重新加载 "概述" 页
* [**删除**](#delete-the-cache)-永久销毁缓存

阅读以下有关这些选项的详细信息。

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

单击 "**升级**" 按钮以开始软件更新。 在操作完成之前，缓存状态将更改为 "正在**升级**"。

## <a name="delete-the-cache"></a>删除缓存

"**删除**" 按钮会销毁缓存。 删除缓存时，它的所有资源都将被销毁，不再产生帐户费用。

当你删除缓存时，用作存储目标的后端存储卷不会受到影响。 稍后可将其添加到将来的缓存，或单独解除其授权。

> [!NOTE]
> 在删除缓存之前，Azure HPC 缓存不会自动将已更改的数据从缓存写入后端存储系统。
>
> 若要确保缓存中的所有数据都已写入到长期存储中，请按照以下步骤进行操作：
>
> 1. 使用 "存储目标" 页上的 "删除" 按钮，从 Azure HPC 缓存中[删除](hpc-cache-edit-storage.md#remove-a-storage-target)每个存储目标。 在删除目标之前，系统会自动将任何已更改的数据从缓存写入后端存储系统。
> 1. 等待存储目标被完全删除。 如果要从缓存中写入大量数据，则该过程可能需要一小时或更长时间。 完成后，门户通知会显示 "删除" 操作已成功，并且存储目标将从列表中消失。
> 1. 删除所有受影响的存储目标后，可以安全地删除缓存。
>
> 或者，您可以使用[flush](#flush-cached-data)选项来保存缓存的数据，但是如果客户端在刷新完成之后但在销毁缓存实例之前将更改写入缓存，则会丢失工作。

## <a name="cache-metrics-and-monitoring"></a>缓存指标和监视

"概述" 页显示一些基本缓存统计信息的图形-缓存吞吐量、每秒操作数和延迟时间。

![显示上述示例缓存的统计信息的三个折线图的屏幕截图](media/hpc-cache-overview-stats.png)

这些图表是 Azure 内置监视和分析工具的一部分。 可以从门户侧栏中 "**监视**" 标题下的页面获得其他工具和警报。 有关详细信息，请[参阅 Azure 监视文档](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal)的门户部分。

## <a name="next-steps"></a>后续步骤

<!-- * Learn more about metrics and statistics for hpc cache -->
* 了解有关[Azure 指标和统计信息工具的](../azure-monitor/index.yml)详细信息
* 获取[有关 AZURE HPC 缓存的帮助](hpc-cache-support-ticket.md)

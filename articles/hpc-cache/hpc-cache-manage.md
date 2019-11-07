---
title: 管理和更新 Azure HPC 缓存
description: 如何使用 Azure 门户管理和更新 Azure HPC 缓存
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 62b54bfe120acdde1fd22c4a0d04165ea7243b50
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582206"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>从 Azure 门户管理缓存

Azure 门户中的 "缓存概述" 页显示缓存的项目详细信息、缓存状态和基本统计信息。 它还包含用于删除缓存、将数据刷新到长期存储或更新软件的控件。

若要打开 "概述" 页，请在 Azure 门户中选择缓存资源。 例如，加载 "**所有资源**" 页，然后单击缓存名称。

![Azure HPC 缓存实例的 "概述" 页的屏幕截图](media/hpc-cache-overview.png) <!-- placeholder is identical to hpc-cache-new-overview.png; replace with better image (showing graphs, full sidebar) when available -->

页面顶部的按钮可帮助你管理缓存：

* [**Flush**](#flush-cached-data) -将所有缓存的数据写入存储目标
* [**升级**](#upgrade-cache-software)-更新缓存软件
* **刷新**-重新加载 "概述" 页
* [**删除**](#delete-the-cache)-永久销毁缓存

阅读以下有关这些选项的详细信息。

## <a name="flush-cached-data"></a>刷新缓存的数据

"概述" 页上的 "**刷新**" 按钮通知缓存将存储在缓存中的所有已更改数据立即写入后端存储目标。 缓存会定期将数据保存到存储目标，因此，无需手动执行此操作，除非你想要确保后端存储系统是最新的。 例如，可以在拍摄存储快照之前使用**Flush** ，或检查数据集的大小。

> [!NOTE]
> 在刷新过程中，缓存无法为客户端请求提供服务。 在操作完成后，缓存访问被挂起并恢复。

当你开始缓存刷新操作时，缓存将停止接受客户端请求，"概述" 页上的缓存状态将更改为 "正在**刷新**"。

缓存中的数据将保存到相应的存储目标。 此过程可能需要几分钟或更长时间，具体取决于最近写入缓存的数据量。

将所有数据保存到存储目标后，该缓存将自动开始重新获取客户端请求。 缓存状态返回为 "**正常**"。

## <a name="upgrade-cache-software"></a>升级缓存软件

如果有新的软件版本，则 "**升级**" 按钮将变为活动状态。 您还可能会在有关更新软件的页面顶部看到一条消息。

![启用了 "升级" 按钮的按钮顶行屏幕截图](media/hpc-cache-upgrade-button.png)

在软件升级过程中，客户端访问不会中断，但缓存性能会降低。 计划在非高峰使用时间或计划内维护期间升级软件。

软件更新可能需要几个小时。 比具有较小的高峰吞吐量值的缓存相比，使用更高吞吐量配置的缓存需要更长的时间来升级

软件升级可用时，你可以在几天内手动应用它。 结束日期将在升级消息中列出。 如果在这段时间内没有升级，Azure 会自动将更新应用于缓存。 自动升级的时间不能配置。 如果你担心会影响缓存性能，则应在此时间段到期之前自行升级软件。

单击 "**升级**" 按钮以开始软件更新。 在操作完成之前，缓存状态将更改为 "正在**升级**"。

## <a name="delete-the-cache"></a>删除缓存

"**删除**" 按钮会销毁缓存。 删除缓存时，它的所有资源都将被销毁，不再产生帐户费用。

删除缓存时，存储目标不会受到影响。 稍后可将其添加到将来的缓存，或单独解除其授权。

缓存会在其最终关闭过程中自动将所有未保存的数据刷新到存储目标。

## <a name="cache-metrics-and-monitoring"></a>缓存指标和监视

"概述" 页显示一些基本缓存统计信息的图形-缓存吞吐量、每秒操作数和延迟时间。

![显示上述示例缓存的统计信息的三个折线图的屏幕截图](media/hpc-cache-overview-stats.png)

这些图表是 Azure 内置监视和分析工具的一部分。 可以从门户侧栏中 "**监视**" 标题下的页面获得其他工具和警报。 有关详细信息，请[参阅 Azure 监视文档](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal)的门户部分。

## <a name="next-steps"></a>后续步骤

<!-- * Learn more about metrics and statistics for hpc cache -->
* 了解有关[Azure 指标和统计信息工具的](../azure-monitor/index.yml)详细信息
* 获取[有关 AZURE HPC 缓存的帮助](hpc-cache-support-ticket.md)

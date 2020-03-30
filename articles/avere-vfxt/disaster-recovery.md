---
title: Azure Avere vFXT 的灾难恢复指南
description: 如何保护 Azure Avere vFXT 中的数据免受意外删除或中断
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 28278f76497d6e9d0fee221bb4ef32fe6d369db0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966652"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Azure Avere vFXT 的灾难恢复指南

本文概述了为 Azure 工作流保护 Avere vFXT 的策略，并提供有关备份数据以便从事故或中断中恢复的指导。

Azure 的 Avere vFXT 临时将数据存储在其缓存中。 数据存储在后端存储系统中长期存储 - 本地硬件系统、Azure Blob 存储容器或两者。

为防止中断和可能的数据丢失，请考虑以下四个方面：

* 如果 Azure 系统的 Avere vFXT 不可用，防止停机
* 保护群集缓存中的数据
* 保护后端 NAS 硬件存储中的数据
* 保护后端 Azure Blob 云存储中的数据

Azure 客户的每个 Avere vFXT 都必须创建自己的全面灾难恢复计划，其中包括这些项目的计划。 您还可以将弹性构建到与 vFXT 群集一起使用的应用程序中。 阅读["后续步骤"](#next-steps)中的链接以寻求帮助。

## <a name="protect-against-downtime"></a>防止停机

冗余内置到 Azure 产品的 Avere vFXT 中：

* 群集高度可用，单个群集节点可以以最小的中断故障进行故障转移。
* 缓存中更改的数据定期写入后端核心文件服务器（硬件 NAS 或 Azure Blob），以便进行长期存储。

Azure 群集的每个 Avere vFXT 都必须位于单个可用性区域中，但您可以使用位于不同区域或不同区域的冗余群集，在区域中断时快速提供访问。

如果您担心无法访问数据，还可以将存储容器放置在多个区域中。 但是，请记住，区域之间的事务比留在区域内的事务具有更高的延迟和更高的成本。

## <a name="protect-data-in-the-cluster-cache"></a>保护群集缓存中的数据

缓存的数据在定期关闭之前始终写入核心文件服务器，但在不受控制的关机中，缓存中更改的数据可能会丢失。

如果仅使用群集优化文件读取，则不会丢失任何更改。 如果还使用群集缓存文件更改（写入），请考虑是否调整核心文件文件的[缓存策略](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html)<!-- link to legacy doc --> 自定义将数据写入长期存储的频率。

通常，您的恢复计划应侧重于备份后端存储系统，这些存储系统包含更多数据，并且通常对于故障后重新建立工作流更为重要。

## <a name="protect-data-in-nas-core-filers"></a>保护 NAS 核心文件服务器中的数据

使用接受的方法保护存储在本地 NAS 硬件核心文件程序中的数据，包括 NAS 提供程序建议的快照和完整备份。 这些核心文件服务器的灾难恢复超出了本文的范围。

## <a name="protect-data-in-azure-blob-storage"></a>保护 Azure Blob 存储中的数据

Azure 的 Avere vFXT 使用 Azure Blob 核心文件服务器的本地冗余存储 （LRS）。 这意味着自动复制 Blob 容器中的数据，以防止数据中心内的瞬态硬件故障。

本节提供有关如何进一步保护 Blob 存储中的数据免受罕见区域范围中断或意外删除的提示。

保护 Azure Blob 存储中数据的最佳做法包括：

* 经常将关键数据复制到另一个区域的另一个存储帐户（通常由灾难恢复计划确定）。
* 控制对所有目标系统上的数据的访问，以防止意外删除或损坏。 请考虑在数据存储上使用[资源锁](../azure-resource-manager/management/lock-resources.md)。
* 为 Blob 核心文件服务器启用适用于 Azure[云快照](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>)功能的 Avere vFXT。

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>将 Avere vFXT 核心文件程序数据复制到备份帐户

按照以下步骤在另一个帐户中建立数据备份。

1. 如果需要，生成新的加密密钥并将其安全地存储在受影响的系统之外。

   如果数据由 Azure 群集的 Avere vFXT 加密，则应在将数据复制到其他存储帐户之前生成新的加密密钥。 将该密钥和密码安全地存储在安全且不会受区域故障影响的设施中。

   将容器添加到群集时必须提供此密钥 ，即使您正在将其重新添加到其原始群集。

   读取[云加密设置](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> 了解详细信息。

   如果容器仅使用 Azure 的内置加密，则可以跳过此步骤。

1. 从系统中删除核心文件服务器。 这强制群集将所有更改的数据写入后端存储。

   尽管您必须在备份后重新添加核心文件程序，但删除它是确保所有数据完全写入后端的最佳方式。 （"挂起"选项有时会将更改的数据保留在缓存中。 <!-- xxx true? or just metadata? -->

   记下核心文件程序的名称和交汇点信息（列在控制面板中的**命名空间**页上），以便在备份后重新添加容器时复制该信息。

   使用群集控制面板删除核心文件程序。 [打开群集控制面板](avere-vfxt-cluster-gui.md)并选择**核心文件服务器** > **管理核心文件程序**。 查找要备份的存储系统，并使用 **"删除**"按钮将其从群集中删除。

1. 在另一个区域中的另一个存储帐户中创建新的空 Blob 存储容器。

1. 使用任何方便的复制工具将核心文件服务器上的数据复制到新容器。 副本必须在不更改的情况下复制数据，并且不会中断 Avere vFXT 为 Azure 使用的专有云文件系统格式。 基于 Azure 的工具包括[AzCopy、Azure](../storage/common/storage-use-azcopy-v10.md) [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)和[Azure 数据工厂](../data-factory/connector-azure-data-lake-store.md)。

1. 将数据复制到备份容器后，将原始容器添加回群集，如[配置存储](avere-vfxt-add-storage.md)中所述。

   * 使用相同的核心文件器名称和交汇点信息，以便客户端工作流无需更改。
   * 将**存储桶内容**值设置为现有数据选项。
   * 如果容器由群集加密，则必须为其内容输入当前加密密钥。 （这是您在步骤一中更新的密钥。

对于第一个备份之后的备份，不需要创建新的存储容器。 但是，请考虑在每次执行备份时生成新的加密密钥，以确保将当前密钥存储在您记得的位置。

### <a name="access-a-backup-data-source-during-an-outage"></a>在中断期间访问备份数据源

要从 Azure 群集的 Avere vFXT 访问备份容器，请按照此过程操作：

1. 如果需要，请为未受影响的区域中的 Azure 群集创建新的 Avere vFXT。

   > [!TIP]
   > 为 Azure 群集创建 Avere vFXT 时，可以保存其创建模板和参数的副本。 如果在创建主群集时保存此信息，则可以使用它创建具有相同属性的替换群集。 在[摘要](avere-vfxt-deploy.md#validation-and-purchase)页上，单击 **"下载"模板和参数**链接。 在创建群集之前，将信息保存到文件中。

1. 添加新的云核心文件器，指向重复的 Blob 容器。

   请确保指定目标容器已在核心文件程序创建向导的 Bucket**内容**设置中包含数据。 （如果您不小心将此集保留为 **"空**"，系统应提醒您。  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. 如有必要，更新客户端，以便它们装载新群集或新核心文件程序，而不是原始文件。 （如果添加与原始容器相同的名称和交汇点路径的替换核心文件程序，则无需更新客户端进程，除非您需要在新的 IP 地址装载新群集。

## <a name="next-steps"></a>后续步骤

* 有关为 Azure 自定义 Avere vFXT 设置的详细信息，请阅读[群集调优](avere-vfxt-tuning.md)。
* 了解有关 Azure 中灾难恢复和构建弹性应用程序的更多信息：

  * [Azure 弹性技术指南](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
  * [发生区域范围的服务中断后进行恢复](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Azure 应用程序的灾难恢复和高可用性](<https://docs.microsoft.com/azure/resiliency/resiliency-disaster-recovery-high-availability-azure-applications>)
  <!-- can't find these in the source tree to use relative links -->

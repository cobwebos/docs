---
title: 业务连续性 - Microsoft 基因组学 | Microsoft Docs
titleSuffix: Azure
description: 了解 Microsoft 基因组学如何为业务连续性提供支持
keywords: 业务连续性, 灾难恢复
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 04/06/2018
ms.openlocfilehash: cb3825cb89aff386c4f7c3f3b0d771d73fe637b1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>使用 Microsoft 基因组学实现业务连续性的概述
本概述介绍 Microsoft 基因组学针对业务连续性和灾难恢复所提供的功能。 了解在发生可能导致数据丢失的中断事件（如 Azure 区域中断）后进行恢复的选项。 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>支持业务连续性的Microsoft 基因组学功能 
在极少见的情况下，Azure 数据中心可能发生中断，从而导致持续数分钟到数小时的业务中断。 发生中断时，数据中心当前正在运行的所有作业都会失败，而 Microsoft 基因组学服务不会自动将作业重新提交到次要区域。 

* 一个选项是在数据中心中断结束后，等待数据中心恢复联机。 如果中断时间较短，Microsoft 基因组学服务会自动检测失败的作业，工作流将自动重启。

* 另一个选项是在另一个数据区域中主动提交工作流。 Microsoft 基因组学在多个 [Azure 区域](https://azure.microsoft.com/regions/services/)中部署实例，每个特定于区域的实例是独立的。 如果某个 Microsoft 基因组学实例遇到区域本地的故障，运行 Microsoft 基因组学实例的其他区域将继续处理作业。 这种转移到备用区域的措施受用户控制，并且随时可用。


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>手动将 Microsoft 基因组学工作流故障转移到另一个区域
如果区域数据中心发生中断，可以选择根据单独的数据主权和业务连续性要求，在次要区域中提交 Microsoft 基因组学工作流。 若要手动故障转移 Microsoft 基因组学工作流，请使用不同的区域特定的 基因组学帐户，并使用相应的区域特定的基因组学和存储帐户凭据提交作业。

具体而言，需要：
* 使用 Azure 门户在次要区域中创建基因组学帐户。 
* 将输入数据迁移到次要区域中的存储帐户，并在次要区域中设置输出文件夹。
* 在次要区域中提交工作流。

还原原始区域后，Microsoft 基因组学服务不会将数据从次要区域迁移回到原始区域。 可以选择将输入和输出文件从次要区域移回到原始区域。  如果选择移动作业的数据，此操作将在基因组学服务的外部进行，与数据移动相关的所有费用由你负责承担。 

### <a name="preparing-for-a-possible-region-specific-outage"></a>准备可能的区域特定中断
如果希望在发生数据中心中断的情况下更快地进行恢复，可以采取多种措施来减少将 Microsoft 基因组学工作流重新提交到次要区域所花费的时间：

* 标识相应的次要区域，并在该区域中主动创建基因组学帐户
* 在主要和次要区域中重复数据，以便数据在次要区域中立即可用。 可以手动或使用 Azure 存储中的[异地冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy)实现此目的。 

## <a name="next-steps"></a>后续步骤
本文介绍了使用 Microsoft 基因组学服务时实现业务连续性和灾难恢复的选项。 有关 Azure 中业务连续性和灾难恢复的一般详细信息，请参阅 [Azure 复原技术指南](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)。 
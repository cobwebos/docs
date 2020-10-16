---
title: Azure Data Lake Storage Gen1 灾难恢复指南 | Microsoft Docs
description: 了解如何在 Azure Data Lake Storage Gen1 的本地冗余存储之外进一步保护数据，避免发生区域范围中断或意外删除。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: 48136f8d9172c3674e849e24efca4ae5070f83ab
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109113"
---
# <a name="high-availability-and-disaster-recovery-guidance-for-data-lake-storage-gen1"></a>Data Lake Storage Gen1 的高可用性和灾难恢复指南

Data Lake Storage Gen1 提供了本地冗余存储 (LRS) 。 因此，Data Lake Storage Gen1 帐户中的数据可通过自动化副本对数据中心内的暂时性硬件故障进行复原。 这可以确保持久性和高可用性，满足 Data Lake Storage Gen1 SLA。 本文提供有关如何进一步保护数据，防范极其少见的全区域服务中断或意外删除行为的一些指导。

## <a name="disaster-recovery-guidance"></a>灾难恢复指南

准备灾难恢复计划至关重要。 查看本文中的信息以及这些其他资源，帮助您创建自己的计划。

* [Azure 应用程序的灾难恢复和高可用性](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Azure 复原技术指南](/azure/architecture/framework/resiliency/overview)

### <a name="best-practice-recommendations"></a>最佳做法建议

我们建议按照与灾难恢复计划需求相符的频率，将关键数据复制到另一个区域中的另一个 Data Lake Storage Gen1 帐户。 有多种方法可以复制数据，包括 [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md)、 [Azure PowerShell](data-lake-store-get-started-powershell.md)或 [Azure 数据工厂](../data-factory/connector-azure-data-lake-store.md)。 Azure 数据工厂是一个非常有用的服务，可以周期性地创建和部署数据移动管道。

若发生区域中断，可在复制数据的区域中访问数据。 可监视 [Azure 服务运行状况仪表板](https://azure.microsoft.com/status/)以确定全球的 Azure 服务状态。

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>数据损坏或意外删除恢复指导

尽管 Data Lake Storage Gen1 通过自动化副本提供数据复原能力，但这不能避免应用程序（或开发者/用户）损坏数据或意外删除数据。

若要防止意外删除，我们建议首先为 Data Lake Storage Gen1 帐户设置适当的访问策略。 这包括应用 [Azure 资源锁](../azure-resource-manager/management/lock-resources.md) 来锁定重要资源，以及使用可用 [Data Lake Storage Gen1 安全功能](data-lake-store-security-overview.md)应用帐户和文件级访问控制。 此外，我们建议在另一个 Data Lake Storage Gen1 帐户、文件夹或 Azure 订阅中，定期使用 [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md)、[Azure PowerShell](data-lake-store-get-started-powershell.md) 或 [Azure 数据工厂](../data-factory/connector-azure-data-lake-store.md)创建关键数据的副本。 发生数据损坏或删除事件时，可以使用这些副本来恢复数据。 Azure 数据工厂是一个非常有用的服务，可以周期性地创建和部署数据移动管道。

你还可以为 Data Lake Storage Gen1 帐户启用 [诊断日志记录](data-lake-store-diagnostic-logs.md) ，以收集数据访问审核跟踪。 审核跟踪提供有关可能已删除或更新文件的用户的信息。

## <a name="next-steps"></a>后续步骤

* [Data Lake Storage Gen1 入门](data-lake-store-get-started-portal.md)
* [保护 Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)
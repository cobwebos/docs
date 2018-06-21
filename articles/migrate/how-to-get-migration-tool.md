---
title: 使用 Azure Migrate 完成评估后迁移计算机 | Microsoft Docs
description: 介绍如何在使用 Azure Migrate 服务运行评估后获取有关迁移计算机的建议。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: 571bd2424d1d38e6c0048a95b263dda000477e44
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221873"
---
# <a name="migrate-machines-after-assessment"></a>在评估后迁移计算机


[Azure Migrate](migrate-overview.md) 可评估本地计算机来检查其是否适合迁移到 Azure，并提供在 Azure 中运行计算机的大小和成本估计。 目前，Azure Migrate 仅评估要迁移的计算机。 目前，迁移本身是使用其他 Azure 服务执行的。

本文介绍在运行迁移评估后如何获取有关迁移工具的建议。

## <a name="migration-tool-suggestion"></a>迁移工具建议

若要获取有关迁移工具的建议，需要对本地环境执行深度发现。 可通过在本地计算机上安装代理来执行深度发现。  

1. 创建 Azure Migrate 项目，发现本地计算机，并创建迁移评估。 [了解详细信息](tutorial-assessment-vmware.md)。
2. 在要查看建议迁移方法的每台本地计算机上下载并安装 Azure Migrate 代理。 [遵循此过程](how-to-create-group-machine-dependencies.md#prepare-machines-for-dependency-mapping)安装代理。
2. 识别适合用于直接迁移的本地计算机。 这些 VM 不需要对其上运行的应用进行任何更改，可按原样迁移。
3. 对于直接迁移，我们建议使用 Azure Site Recovery。 [了解详细信息](../site-recovery/tutorial-migrate-on-premises-to-azure.md)。 或者，可以使用支持迁移到 Azure 的第三方工具。
4. 如果本地计算机不适合直接迁移，即，如果想要迁移特定的应用而不是整个 VM，可以使用其他迁移工具。 例如，如果想要将 SQL Server、MySQL 或 Oracle 等本地数据库迁移到 Azure，我们建议使用 [Azure 数据库迁移服务](https://azure.microsoft.com/campaigns/database-migration/)


## <a name="review-suggested-migration-methods"></a>查看建议的迁移方法

1. 在可以获取建议的迁移方法之前，需要创建 Azure 迁移项目，发现本地计算机并运行迁移评估。 [了解详细信息](tutorial-assessment-vmware.md)。
2. 创建评估后，可在项目“项目”>“概述” > “仪表板”中查看该评估。 单击“评估就绪”

    ![评估就绪](./media/tutorial-assessment-vmware/assessment-report.png)  

3. 在“建议的工具”中，查看可用于迁移的工具的建议。

    ![建议的工具](./media/tutorial-assessment-vmware/assessment-suitability.png) 




## <a name="next-steps"></a>后续步骤

[详细了解](concepts-assessment-calculation.md)如何计算评估。

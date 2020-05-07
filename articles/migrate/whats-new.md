---
title: Azure Migrate 中的新增功能
description: 了解 Azure Migrate 服务中的新增功能和最新更新。
ms.topic: overview
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: ba85b0a4ed30320099388ccb48dd91ad6445efd5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81677248"
---
# <a name="whats-new-in-azure-migrate"></a>Azure Migrate 中的新增功能

[Azure Migrate](migrate-services-overview.md) 可帮助发现和访问本地服务器、应用和数据并将其迁移到 Microsoft Azure 云。 本文汇总了 Azure Migrate 中的新版本和功能。


## <a name="update-april-2020"></a>更新（2020 年 4 月）

Azure Migrate 支持在 Azure 政府中部署。 

- 你可以发现和评估 VMware VM、Hyper-V VM 和物理服务器。
- 你可以将 VMware VM、Hyper-V VM 和物理服务器迁移到 Azure。
- 对于 VMware 迁移，可以使用无代理或基于代理的迁移。 [了解详细信息](server-migrate-overview.md)。
- [查看](migrate-support-matrix.md#supported-geographies-azure-government) Azure 政府支持的地理位置和区域。
- Azure 政府不支持[基于代理的依赖关系分析](concepts-dependency-visualization.md#agent-based-analysis)。
- Azure 政府支持预览版中的功能，特别是[无代理依赖关系分析](concepts-dependency-visualization.md#agentless-analysis)和[应用程序发现](how-to-discover-applications.md)。

## <a name="update-march-2020"></a>更新（2020 年 3 月）

基于脚本的安装现在可用于设置 [Azure Migrate 设备](migrate-appliance.md)：

- 基于脚本的安装可替代设备的 .OVA (VMware)/VHD (Hyper-V) 安装。
- 它提供 PowerShell 安装程序脚本，该脚本可用于在运行 Windows Server 2016 的现有计算机上为 VMware/Hyper-V 设置设备。

## <a name="update-november-2019"></a>更新（2019 年 11 月）

Azure Migrate 增加了许多新功能：

- **物理服务器评估**。 除了已支持的物理服务器迁移外，现在还支持对本地物理服务器进行评估。
- **基于导入的评估**。 现在支持使用 CSV 文件中提供的元数据和性能数据评估计算机。
- **应用程序发现**：Azure Migrate 现在支持使用 Azure Migrate 设备实现应用、角色和功能的应用程序级发现。 目前仅适用于 VMware VM，并且仅限于发现（目前不支持评估）。 [了解详细信息](how-to-discover-applications.md)
- **无代理依赖项可视化效果**：不再需要为依赖项可视化效果显式安装代理。 现在同时支持无代理和基于代理。
- **虚拟桌面**：使用 ISV 工具评估本地虚拟桌面基础结构 (VDI) 并将其迁移到 Azure 中的 Windows 虚拟桌面。
- **Web 应用**：用于评估和迁移 Web 应用的 Azure 应用服务迁移助手现已集成到 Azure Migrate 中。

Azure Migrate 中提供了新的评估和迁移工具：

- **Rackware**：提供云迁移功能。
- **Movere**：提供评估功能。

[详细了解](migrate-services-overview.md)如何使用 Azure Migrate 中用于评估与迁移的工具和 ISV 产品/服务。

## <a name="azure-migrate-current-version"></a>Azure Migrate 当前版本

Azure Migrate 的当前版本（在 2019 年 7 月发布）提供许多新功能：

- **统一迁移平台**：Azure Migrate 现在提供单个门户，可以集中、管理和跟踪到 Azure 的迁移旅程，并改进了部署流和门户体验。
- **评估和迁移工具**：Azure Migrate 提供本机工具，并与其他 Azure 服务以及独立软件供应商 (ISV) 工具集成。 [详细了解](migrate-services-overview.md#isv-integration) ISV 集成。
- **Azure Migrate 评估**：Azure Migrate 服务器评估可评估 VMware VM 和 Hyper-V VM，以便迁移到 Azure。 还可以使用其他 Azure 服务和 ISV 工具评估迁移。
- **Azure Migrate 迁移**：使用 Azure Migrate 服务迁移工具，可以将本地 VMware VM、Hyper-v VM、物理服务器、其他虚拟服务器和专用/公有云 VM 迁移到 Azure。 此外，还可以使用 ISV 工具迁移到 Azure。
- **Azure Migrate 设备**：Azure Migrate 部署轻型设备，用于发现和评估本地的 VMware VM 和 Hyper-V VM。
    - Azure Migrate 服务器评估和 Azure Migrate 服务器迁移使用此设备进行无代理迁移。
    - 设备持续发现服务器元数据和性能数据，以进行评估和迁移。  
- **VMware VM 迁移**：Azure Migrate 服务器迁移提供几种将本地 VMware Vm 迁移到 Azure 的方法。  使用 Azure Migrate 设备的无代理迁移，以及使用复制设备且在要迁移的每个 VM 上部署代理的基于代理的迁移。 [了解详细信息](server-migrate-overview.md)
 - **数据库评估和迁移**：在 Azure Migrate 中，可使用 Azure 数据库迁移助手来评估要迁移到 Azure 的本地数据库。 使用 Azure 数据库迁移服务来迁移数据库。
- **Web 应用迁移**：可以通过 Azure 应用服务，使用公共终结点 URL 来评估 Web 应用。 若要迁移内部 .NET 应用，可以下载并运行应用服务迁移助手。
- **Data Box**：使用 Azure Migrate 中的 Azure Data Box 将大量脱机数据导入到 Azure。

## <a name="azure-migrate-previous-version"></a>Azure Migrate 旧版本

如果你使用的是以前版本的 Azure Migrate（仅支持对本地 VMware VM 的评估），则现在应使用当前版本。 在以前的版本中，已无法创建新的 Azure Migrate 项目，也无法执行新发现。 仍可以访问现有项目。 在“Azure 门户”>“所有服务”中执行此操作，搜索“Azure Migrate”   。 Azure Migrate 通知中提供了一个用于访问旧 Azure Migrate 项目的链接。



## <a name="next-steps"></a>后续步骤

- [详细了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定价。
- [查看常见问题解答](resources-faq.md)（关于 Azure Migrate）。
- 试用我们的教程来评估 [VMware VM](tutorial-assess-vmware.md) 和 [Hyper-V VM](tutorial-assess-hyper-v.md)。

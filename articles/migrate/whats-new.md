---
title: Azure Migrate 中的新增功能
description: 了解 Azure Migrate 服务中的新增功能和最新更新。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 63b6783a2f36d5bc9e84ce8291e7025b27359b6c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361802"
---
# <a name="whats-new-in-azure-migrate"></a>Azure Migrate 中的新增功能

[Azure Migrate](migrate-services-overview.md) 可帮助发现和访问本地服务器、应用和数据并将其迁移到 Microsoft Azure 云。 本文汇总了 Azure Migrate 中的新增功能。



## <a name="update-november-2019"></a>更新（2019 年 11 月）

Azure Migrate 增加了许多新功能：

- **物理服务器评估**。 除了已支持的物理服务器迁移外，现在还支持对本地物理服务器进行评估。
- **基于导入的评估**。 现在支持使用 CSV 文件中提供的元数据和性能数据评估计算机。
- **应用程序发现**： Azure Migrate 现在支持使用 Azure Migrate 设备的应用、角色和功能的应用程序级发现。 目前仅适用于 VMware VM，并且仅限于发现（目前不支持评估）。 [了解详细信息](how-to-discover-applications.md)
- **无代理依赖项可视化**：不再需要显式安装用于依赖项可视化的代理。 现在同时支持无代理和基于代理。
- **虚拟桌面**：使用 ISV 工具来评估本地虚拟桌面基础结构（VDI）并将其迁移到 Azure 中的 Windows 虚拟桌面。
- **Web 应用**：用于评估和迁移 web 应用的 Azure App Service 迁移助手现已集成到 Azure Migrate 中。

Azure Migrate 中提供了新的评估和迁移工具：

- **Rackware**：提供云迁移。
- **Movere**：产品评估。

[详细了解](migrate-services-overview.md)如何使用 Azure Migrate 中用于评估与迁移的工具和 ISV 产品/服务。

## <a name="release-version-july-2019"></a>发行版（2019 年 7 月）

2019 年 7 月发布了 Azure Migrate 的当前版本。

- **当前版本**：使用此版本创建 Azure Migrate 项目，发现本地计算机并协调评估和迁移。
- **以前的版本**：对于使用 Azure Migrate 的以前版本的客户（仅支持对本地 VMware vm 进行评估），你现在应该使用当前版本。 在以前的版本中，已无法创建新的 Azure Migrate 项目，也无法执行新发现。 仍可以访问现有项目。 在“Azure 门户”>“所有服务”中执行此操作，搜索“Azure Migrate”。 Azure Migrate 通知中提供了一个用于访问旧 Azure Migrate 项目的链接。


### <a name="azure-migrate-features"></a>Azure Migrate 功能

Azure Migrate 的当前版本提供许多新功能：


- **统一迁移平台**： Azure Migrate 现在提供单个门户来集中、管理和跟踪迁移到 Azure 的过程，并改进了部署流和门户体验。
- **评估和迁移工具**： Azure Migrate 提供本机工具，并与其他 Azure 服务和独立软件供应商（ISV）工具集成。 [详细了解](migrate-services-overview.md#isv-integration) ISV 集成。
- **Azure Migrate 评估**：使用 Azure Migrate 服务器评估工具，你可以评估要迁移到 Azure 的 VMware Vm 和 hyper-v vm。 还可以使用其他 Azure 服务和 ISV 工具评估迁移。
- **Azure Migrate 迁移**：通过使用 Azure Migrate 服务器迁移工具，你可以将本地 VMware Vm 和 hyper-v vm 迁移到 Azure，以及物理服务器、其他虚拟化服务器和专用/公有云 vm。 此外，还可以使用 ISV 工具迁移到 Azure。
- **Azure Migrate 设备**： Azure Migrate 部署一个轻型设备，用于发现和评估本地 VMware Vm 和 hyper-v vm。
    - Azure Migrate 服务器评估和 Azure Migrate 服务器迁移使用此设备进行无代理迁移。
    - 设备持续发现服务器元数据和性能数据，以进行评估和迁移。  
- **VMWARE VM 迁移**： Azure Migrate Server 迁移提供了几种方法用于将本地 VMware vm 迁移到 Azure。  使用 Azure Migrate 设备的无代理迁移，以及使用复制设备且在要迁移的每个 VM 上部署代理的基于代理的迁移。 [了解详细信息](server-migrate-overview.md)
 - **数据库评估和迁移**：从 Azure Migrate 中，可以使用 Azure 数据库迁移助手评估用于迁移到 azure 的本地数据库。 使用 Azure 数据库迁移服务来迁移数据库。
- **Web 应用迁移**：你可以使用具有 Azure App Service 的公共终结点 URL 来评估 Web 应用。 若要迁移内部 .NET 应用，可以下载并运行应用服务迁移助手。
- **Data Box**：使用 Azure Migrate 中的 Azure Data Box 将大量脱机数据导入到 Azure。


## <a name="next-steps"></a>后续步骤

- [详细了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定价。
- [查看常见问题解答](resources-faq.md)（关于 Azure Migrate）。
- 试用我们的教程来评估 [VMware VM](tutorial-assess-vmware.md) 和 [Hyper-V VM](tutorial-assess-hyper-v.md)。

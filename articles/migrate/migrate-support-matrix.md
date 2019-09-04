---
title: Azure Migrate 支持矩阵
description: 概述 Azure Migrate 服务的支持设置和限制。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: raynew
ms.openlocfilehash: 3fb36eb9b8507903d1aee00b82c375685a4dfa6c
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279475"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate 支持矩阵

你可以使用[Azure Migrate 服务](migrate-overview.md)来评估计算机并将其迁移到 Microsoft Azure 云。 本文总结了 Azure Migrate 方案和部署的一般支持设置和限制。


## <a name="azure-migrate-versions"></a>Azure Migrate 版本

有两个版本的 Azure Migrate 服务：

- **当前版本**：使用此版本，你可以创建新的 Azure Migrate 项目，发现本地评估并协调评估和迁移。 [了解详细信息](whats-new.md#azure-migrate-new-version)。
- **以前版本**：如果客户使用的是以前版本的 Azure Migrate（仅支持对本地 VMware VM 的评估），则现在应使用当前版本。 在以前的版本中，不能创建新的 Azure Migrate 项目或执行新的发现。

## <a name="supported-migration-scenarios"></a>支持的迁移方案

下表汇总了支持的迁移方案。

**部署** | **详细信息** 
--- | --- 
**本地评估** | 评估 VMware Vm 和 Hyper-v Vm 上运行的本地工作负荷和数据。 使用 Azure Migrate Server 评估和 Microsoft 数据迁移助手（DMA）以及第三方工具（包括 Cloudamize、Corent Tech 和 Turbonomic 服务器）进行评估。
**本地迁移到 Azure** | 将物理服务器、VMware Vm、Hyper-v Vm、物理服务器以及基于云的虚拟机上运行的工作负荷和数据迁移到 Azure。 使用 Azure Migrate Server 评估和 Azure 数据库迁移服务（DMS）迁移，并使用包含 Carbonite 和 CorentTech 的第三方工具。

下面总结了特定工具支持。

**工具** | **评估/迁移** | **详细信息**
--- | --- | ---
Azure Migrate 服务器评估 | 评估 | 试用适用于[hyper-v](tutorial-prepare-hyper-v.md)和[VMware](tutorial-prepare-vmware.md)的服务器评估。
Cloudamize | 评估 | [了解详细信息](https://www.cloudamize.com/platform#tab-0)。
CorentTech | 评估 | [了解详细信息](https://www.corenttech.com/)。
Turbonomic | 评估 | [了解详细信息](https://turbonomic.com/solutions/technologies/azure-cloud/)。
Azure Migrate 服务器迁移 | 迁移 | 试用适用于[hyper-v](tutorial-migrate-hyper-v.md)和[VMware](tutorial-migrate-vmware.md)的服务器迁移。
Carbonite | 迁移 | [了解详细信息](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure)。
CorentTech | 迁移 | [了解详细信息](https://www.corenttech.com/)。


## <a name="azure-migrate-projects"></a>Azure Migrate 项目

**支持** | **详细信息**
--- | ---
订阅 | 一个订阅中可以有多个 Azure Migrate 项目。
Azure 权限 | 你需要订阅中的 "参与者" 或 "所有者" 权限才能创建 Azure Migrate 项目。
VMware VM  | 在单个项目中最多评估35000个 VMware Vm。
Hyper-V VM | 在单个项目中最多评估35000个 Hyper-v Vm。

项目可以包括 VMware Vm 和 Hyper-v Vm，最高可达评估限制。


## <a name="vmware-assessment-and-migration"></a>VMware 评估和迁移

[查看](migrate-support-matrix-vmware.md)VMware vm 的 Azure Migrate 服务器评估和服务器迁移支持表。

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V 评估和迁移

[查看](migrate-support-matrix-hyper-v.md)适用于 hyper-v vm 的 Azure Migrate 服务器评估和服务器迁移支持矩阵。


## <a name="next-steps"></a>后续步骤

- [评估 VMware vm](tutorial-assess-vmware.md)以进行迁移。
- [评估 Hyper-v vm](tutorial-assess-hyper-v.md)以进行迁移。


---
title: Azure Migrate 支持矩阵
description: 概述 Azure Migrate 服务的支持设置和限制。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: raynew
ms.openlocfilehash: 18032250bc5c321d638ad46204738f49f1a0c744
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73480140"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate 支持矩阵

你可以使用[Azure Migrate 服务](migrate-overview.md)来评估计算机并将其迁移到 Microsoft Azure 云。 本文总结了 Azure Migrate 方案和部署的一般支持设置和限制。


## <a name="azure-migrate-versions"></a>Azure Migrate 版本

有两个版本的 Azure Migrate 服务：

- **当前版本**：通过使用此版本，你可以创建新的 Azure Migrate 项目，发现本地评估并协调评估和迁移。 [了解详细信息](whats-new.md#release-version-july-2019)。
- **以前的版本**：对于使用 Azure Migrate 的以前版本的客户（仅支持对本地 VMware vm 进行评估），你现在应该使用当前版本。 在以前的版本中，不能创建新的 Azure Migrate 项目或执行新的发现。

## <a name="supported-assessmentmigration-scenarios"></a>支持的评估/迁移方案

下表汇总了支持的发现、评估和迁移方案。

**部署** | **详细信息** 
--- | --- 
**特定于应用的发现** | 你可以发现在 VMware Vm 上运行的应用、角色和功能。 当前此功能仅限于发现。 评估目前处于计算机级别。 我们尚未提供应用、角色或功能特定的评估。 
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

## <a name="supported-geographies"></a>支持的地理位置

你可以在多个地理区域中创建 Azure Migrate 项目。 尽管你只能在这些地理位置创建项目，但你可以为其他目标位置评估或迁移计算机。 项目地域仅用于存储已发现的元数据。

**地域** | **元数据存储位置**
--- | ---
Azure Government | 美国政府弗吉尼亚州
亚太区 | 东亚或东南亚
澳大利亚 | 澳大利亚东部或澳大利亚东南部
巴西 | 巴西南部
加拿大 | 加拿大中部或加拿大东部
欧洲 | 欧洲北部或欧洲西部
法国 | 法国中部
印度 | 印度中部或印度南部
日本 |  日本东部或日本西部
韩国 | 韩国中部或韩国南部
英国 | 英国南部或英国西部
美国 | 美国中部或美国西部2


 > [!NOTE]
 > Azure 政府版支持目前仅适用于[较旧版本](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)的 Azure Migrate。



## <a name="vmware-assessment-and-migration"></a>VMware 评估和迁移

[查看](migrate-support-matrix-vmware.md)VMware vm 的 Azure Migrate 服务器评估和服务器迁移支持表。

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V 评估和迁移

[查看](migrate-support-matrix-hyper-v.md)适用于 hyper-v vm 的 Azure Migrate 服务器评估和服务器迁移支持矩阵。


## <a name="next-steps"></a>后续步骤

- [评估 VMware vm](tutorial-assess-vmware.md)以进行迁移。
- [评估 Hyper-v vm](tutorial-assess-hyper-v.md)以进行迁移。


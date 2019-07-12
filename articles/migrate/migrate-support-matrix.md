---
title: 迁移的 azure 支持矩阵
description: 提供的支持设置和 Azure Migrate 服务限制的摘要。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: raynew
ms.openlocfilehash: b2ca1b9118ecc3d112a49bb4c79b413c46fe67cb
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811552"
---
# <a name="azure-migrate-support-matrix"></a>迁移的 azure 支持矩阵

可以使用[Azure Migrate 服务](migrate-overview.md)来评估和将计算机迁移到 Microsoft Azure 云。 本文汇总了常规支持设置和 Azure Migrate 方案和部署的限制。


## <a name="azure-migrate-versions"></a>Azure Migrate 版本

有两个版本的 Azure Migrate 服务：

- **当前版本**:使用此版本，可以创建新 Azure Migrate 项目，发现本地评估，并安排评估和迁移。 [了解详细信息](whats-new.md#azure-migrate-new-version)。
- **以前的版本**:对于使用 Azure Migrate （支持评估本地 VMware Vm） 的以前版本的客户，现在应使用最新版本。 在上一版本中，无法创建新的 Azure Migrate 项目或执行新的发现。

## <a name="supported-migration-scenarios"></a>支持的迁移方案

表汇总了支持的迁移方案。

**部署** | **详细信息*** 
--- | --- 
**在本地评估** | 评估本地工作负荷和 VMware Vm 和 HYPER-V Vm 上运行的数据。 评估 Azure Migrate Server 评估和 Microsoft 数据迁移助手 (DMA)，以及第三方工具，包括 Cloudamize、 Corent 技术和 Turbonomic 服务器使用。
**在本地迁移到 Azure** | 迁移工作负荷和物理服务器、 VMware Vm、 HYPER-V Vm 和 AWS/GCP 实例，迁移到 Azure 上运行的数据。 使用 Azure Migrate Server 评估和 Azure 数据库迁移服务 (DMS)，并迁移以及使用第三方工具，包括 Carbonite 和 CorentTech。

按如下所示总结了特定的工具支持。

**工具** | **评估/迁移** | **详细信息**
--- | --- | ---
Azure Migrate Server 评估 | 评估 | 尝试为 server 评估[HYPER-V](tutorial-prepare-hyper-v.md)并[VMware](tutorial-prepare-vmware.md)。
Cloudamize | 评估 | [了解详细信息](https://www.cloudamize.com/platform#tab-0)。
CorentTech | 评估 | [了解详细信息](https://www.corenttech.com/)。
Turbonomic | 评估 | [了解详细信息](https://turbonomic.com/solutions/technologies/azure-cloud/)。
Azure 迁移服务器迁移 | 迁移 | 试用的服务器迁移[HYPER-V](tutorial-migrate-hyper-v.md)并[VMware](tutorial-migrate-vmware.md)。
Carbonite | 迁移 | [了解详细信息](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure)。
CorentTech | 迁移 | [了解详细信息](https://www.corenttech.com/)。


## <a name="azure-migrate-projects"></a>Azure Migrate 项目

**支持** | **详细信息**
--- | ---
订阅 | 在订阅中可以单个 Azure Migrate 项目。
Azure 权限 | 需要参与者或所有者的权限的订阅中创建一个 Azure Migrate 项目。
VMware VM  | 评估最多 35,000 单个项目中的 VMware Vm。
Hyper-V VM | 评估最多 10,000 个单个项目中的 HYPER-V Vm。

项目可以包括 VMware Vm 和 HYPER-V 虚拟机，直到达到评估限制为止。


## <a name="vmware-assessment-and-migration"></a>VMware 评估和迁移

[查看](migrate-support-matrix-vmware.md)Azure Migrate Server 评估和服务器迁移 VMware Vm 的支持矩阵。

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V 评估和迁移

[查看](migrate-support-matrix-hyper-v.md)Azure Migrate Server 评估和服务器迁移的 HYPER-V Vm 的支持矩阵。


## <a name="next-steps"></a>后续步骤

- [评估 VMware Vm](tutorial-assess-vmware.md)进行迁移。
- [评估 HYPER-V Vm](tutorial-assess-hyper-v.md)进行迁移。


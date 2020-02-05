---
title: Azure Migrate 支持矩阵
description: 概述 Azure Migrate 服务的支持设置和限制。
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: raynew
ms.openlocfilehash: 81939292885e33ec6397eb4a437bef63b4ad0787
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990719"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate 支持矩阵

你可以使用[Azure Migrate 服务](migrate-overview.md)来评估计算机并将其迁移到 Microsoft Azure 云。 本文总结了 Azure Migrate 方案和部署的一般支持设置和限制。

## <a name="supported-assessmentmigration-scenarios"></a>支持的评估/迁移方案

下表汇总了支持的发现、评估和迁移方案。

**部署** | **详细信息** 
--- | --- 
**特定于应用的发现** | 你可以发现在 VMware Vm 上运行的应用、角色和功能。 当前此功能仅限于发现。 评估目前处于计算机级别。 我们尚未提供应用、角色或功能特定的评估。 
**本地评估** | 评估 VMware Vm、Hyper-v Vm 和物理服务器上运行的本地工作负荷和数据。 使用 Azure Migrate Server 评估和 Microsoft 数据迁移助手（DMA）以及其他工具和 ISV 产品进行评估。
**本地迁移到 Azure** | 将物理服务器、VMware Vm、Hyper-v Vm、物理服务器以及基于云的虚拟机上运行的工作负荷和数据迁移到 Azure。 使用 Azure Migrate Server 评估和 Azure 数据库迁移服务（DMS）以及其他工具和 ISV 产品/服务进行迁移。


## <a name="supported-tools"></a>支持的工具

表中汇总了特定工具支持。

**工具** | **评估** | **迁移** 
--- | --- | ---
Azure Migrate 服务器评估 | 评估[VMware vm](tutorial-prepare-vmware.md)、 [hyper-v vm](tutorial-prepare-hyper-v.md)和[物理服务器](tutorial-prepare-physical.md)。 |  不可用（NA）
Azure Migrate 服务器迁移 | 不可用 | 迁移[VMware vm](tutorial-migrate-vmware.md)、 [hyper-v vm](tutorial-migrate-hyper-v.md)和[物理服务器](tutorial-migrate-physical-virtual-machines.md)。
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | 不可用 | 迁移 VMware Vm、Hyper-v Vm、物理服务器、公有云工作负荷。 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| 评估 VMware Vm、Hyper-v Vm、物理服务器、公有云工作负荷。 | 不可用
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | 评估和迁移 VMware Vm、Hyper-v Vm、物理服务器、公有云工作负荷。 |  迁移 VMware Vm、Hyper-v Vm、物理服务器、公有云工作负荷。
[设备 42](https://go.microsoft.com/fwlink/?linkid=2097158) | 评估 VMware Vm、Hyper-v Vm、物理服务器、公有云工作负荷。| 不可用
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | 评估本地 SQL Server 数据库。 | 不可用
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | 不可用 | 迁移 SQL Server、Oracle、MySQL、PostgreSQL、MongoDB。 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 评估虚拟桌面基础结构（VDI） | 不可用
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | 评估 VMWare Vm、Hyper-v Vm、Xen Vm、物理计算机、工作站（包括 VDI）、公有云工作负荷 | 不可用
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | 不可用 | 迁移 VMWare Vm、Hyper-v Vm、Xen Vm、KVM Vm、物理计算机、公有云工作负荷 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | 评估 VMware Vm、Hyper-v Vm、物理服务器、公有云工作负荷。 | 不可用
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | 评估 VMware Vm、Hyper-v Vm、物理服务器、公有云工作负荷和 SQL Server 数据库。 | 不可用
[Webapp 迁移助手](https://appmigration.microsoft.com/) | 评估 web 应用 | 迁移 web 应用。


## <a name="azure-migrate-projects"></a>Azure Migrate 项目

**支持** | **详细信息**
--- | ---
订阅 | 一个订阅中可以有多个 Azure Migrate 项目。
Azure 权限 | 你需要订阅中的 "参与者" 或 "所有者" 权限才能创建 Azure Migrate 项目。
VMware VM  | 在单个项目中最多评估35000个 VMware Vm。
Hyper-V VM | 在单个项目中最多评估35000个 Hyper-v Vm。

项目可以包括 VMware Vm 和 Hyper-v Vm，最高可达评估限制。

## <a name="azure-permissions"></a>Azure 权限

要使 Azure Migrate 可以使用 Azure，请在开始评估和迁移计算机之前，需要这些权限。

**任务** | **权限** | **详细信息**
--- | --- | ---
创建 Azure Migrate 项目 | Azure 帐户需要创建项目的权限。 | 为[VMware](tutorial-prepare-vmware.md#assign-permissions-to-create-project)、 [hyper-v](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)或[物理服务器](tutorial-prepare-physical.md#assign-permissions-to-create-project)进行设置。
注册 Azure Migrate 设备 | Azure Migrate 使用轻型[Azure Migrate 设备](migrate-appliance.md)通过 Azure Migrate 服务器评估来评估 vmware vm，并使用 Azure Migrate 服务器迁移运行 vmware vm 的[无代理迁移](server-migrate-overview.md)。 此设备发现 VM，并将 VM 元数据和性能数据发送到 Azure Migrate。<br/><br/> 在注册过程中，Azure Migrate 将创建两个 Azure Active Directory 域服务 (Azure AD) 应用用于唯一标识设备，并需要权限来创建这些应用。<br/><br/> - 第一个应用将与 Azure Migrate 服务终结点通信。<br/><br/> - 第二个应用访问注册期间创建的 Azure Key Vault，以存储 Azure AD 应用信息和设备配置设置。 | 为[VMware](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance)、 [hyper-v](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)或[物理服务器](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance)进行设置。
创建用于 VMware 无代理迁移的密钥保管库 | 若要使用无代理 Azure Migrate 服务器迁移迁移 VMware Vm，Azure Migrate 创建 Key Vault，以管理订阅中复制存储帐户的访问密钥。 若要创建保管库，请在 Azure Migrate 项目所在的资源组上设置权限（所有者或参与者和用户访问管理员）。 | [设置](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault)权限。

## <a name="supported-geographies"></a>支持的地理位置

你可以在多个地理区域中创建 Azure Migrate 项目。 尽管你只能在这些地理位置创建项目，但你可以为其他目标位置评估或迁移计算机。 项目地域仅用于存储已发现的元数据。

**地域** | **元数据存储位置**
--- | ---
Azure Government | 美国弗吉尼亚州政府
亚太 | 东亚或东南亚
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



## <a name="azure-migrate-versions"></a>Azure Migrate 版本

有两个版本的 Azure Migrate 服务：

- **当前版本**：通过使用此版本，你可以创建新的 Azure Migrate 项目，发现本地评估并协调评估和迁移。 [了解详细信息](whats-new.md#release-version-july-2019)。
- **以前的版本**：对于使用 Azure Migrate 的以前版本的客户（仅支持对本地 VMware vm 进行评估），你现在应该使用当前版本。 在以前的版本中，不能创建新的 Azure Migrate 项目或执行新的发现。

## <a name="next-steps"></a>后续步骤

- [评估 VMware vm](tutorial-assess-vmware.md)以进行迁移。
- [评估 Hyper-v vm](tutorial-assess-hyper-v.md)以进行迁移。


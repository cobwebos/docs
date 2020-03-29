---
title: Azure 迁移支持矩阵
description: 提供 Azure 迁移服务的支持设置和限制摘要。
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: raynew
ms.openlocfilehash: bf719f9179384ec3dca99d2429f569ef209b5daa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127701"
---
# <a name="azure-migrate-support-matrix"></a>Azure 迁移支持矩阵

可以使用 Azure[迁移服务](migrate-overview.md)评估和将计算机迁移到 Microsoft Azure 云。 本文总结了 Azure 迁移方案和部署的一般支持设置和限制。

## <a name="supported-assessmentmigration-scenarios"></a>支持的评估/迁移方案

下表总结了支持的发现、评估和迁移方案。

**部署** | **详细信息** 
--- | --- 
**特定于应用的发现** | 您可以发现在 VMware VM 上运行的应用、角色和功能。 目前，此功能仅限于发现。 评估目前处于机器级别。 我们尚未提供应用、角色或特定于功能的评估。 
**本地评估** | 评估在 VMware VM、超 VM 和物理服务器上运行的本地工作负载和数据。 使用 Azure 迁移服务器评估和 Microsoft 数据迁移助理 （DMA） 以及其他工具和 ISV 产品进行评估。
**本地迁移到 Azure** | 将物理服务器、VMware VM、超 VM、物理服务器和基于云的 VMS 上运行的工作负载和数据迁移到 Azure。 使用 Azure 迁移服务器评估和 Azure 数据库迁移服务 （DMS） 以及其他工具和 ISV 产品进行迁移。


## <a name="supported-tools"></a>支持的工具

下表汇总了特定工具支持。

**工具** | **评估** | **迁移** 
--- | --- | ---
Azure Migrate 服务器评估 | 评估[VMware VM、](tutorial-prepare-vmware.md)[超 VM](tutorial-prepare-hyper-v.md)和[物理服务器](tutorial-prepare-physical.md)。 |  不可用 （NA）
Azure Migrate 服务器迁移 | NA | 迁移[VMware VM、](tutorial-migrate-vmware.md)[超 VM](tutorial-migrate-hyper-v.md)和[物理服务器](tutorial-migrate-physical-virtual-machines.md)。
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | NA | 迁移 VMware VM、超虚拟机、物理服务器和公共云工作负载。 
[云雾化](https://www.cloudamize.com/platform#tab-0)| 评估 VMware VM、超虚拟机、物理服务器、公共云工作负载。 | NA
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | 评估和迁移 VMware VM、超虚拟机、物理服务器、公共云工作负载。 |  迁移 VMware VM、超虚拟机、物理服务器和公共云工作负载。
[设备 42](https://go.microsoft.com/fwlink/?linkid=2097158) | 评估 VMware VM、超虚拟机、物理服务器、公共云工作负载。| NA
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | 评估本地 SQL Server 数据库。 | NA
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | NA | 迁移 SQL 服务器、Oracle、MySQL、PostgreSQL、MongoDB。 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 评估虚拟桌面基础架构 （VDI） | NA
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | 评估 VMWare VM、超 VM、Xen VM、物理计算机、工作站（包括 VDI）、公共云工作负载 | NA
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | NA | 迁移 VMWare VM、超 VM、Xen VM、KVM、计算机虚拟机、物理计算机、公共云工作负载 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | 评估 VMware VM、超虚拟机、物理服务器、公共云工作负载。 | NA
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | 评估 VMware VM、超 VM、物理服务器、公共云工作负载和 SQL Server 数据库。 | NA
[Webapp 迁移助手](https://appmigration.microsoft.com/) | 评估 Web 应用 | 迁移 Web 应用。


## <a name="azure-migrate-projects"></a>Azure 迁移项目

**支持** | **详细信息**
--- | ---
订阅 | 订阅中可以有多个 Azure 迁移项目。
Azure 权限 | 您需要订阅中的参与者或所有者权限才能创建 Azure 迁移项目。
VMware VM  | 在单个项目中评估多达 35，000 个 VMware VM。
Hyper-V VM    | 在单个项目中评估多达 35，000 台 Hyper-V VM。

项目可以同时包括 VMware VM 和超 V VM，但达到评估限制。

## <a name="azure-permissions"></a>Azure 权限

对于 Azure 迁移以使用 Azure，在开始评估和迁移计算机之前，需要这些权限。

**任务** | **权限** | **详细信息**
--- | --- | ---
创建 Azure Migrate 项目 | Azure 帐户需要创建项目的权限。 | 为[VMware、](tutorial-prepare-vmware.md#assign-permissions-to-create-project)[超 V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)或[物理服务器](tutorial-prepare-physical.md#assign-permissions-to-create-project)设置。
注册 Azure Migrate 设备| Azure 迁移使用轻量级[Azure 迁移设备](migrate-appliance.md)评估具有 Azure 迁移服务器评估的计算机，并使用 Azure 迁移服务器迁移运行 VMware VM[的无代理迁移](server-migrate-overview.md)。 本设备发现计算机，并将元数据和性能数据发送到 Azure 迁移。<br/><br/> 在注册期间，注册提供程序（Microsoft.OffAzure、Microsoft.Migrate 和 Microsoft.KeyVault）在设备中选择的订阅中注册，以便订阅与资源提供程序一起工作。 要注册，您需要订阅上的参与者或所有者访问权限。<br/><br/> **VMware**- 在载入过程中，Azure 迁移将创建两个 Azure 活动目录 （Azure AD） 应用。 第一个应用在设备代理和 Azure 迁移服务之间通信。 应用没有对 Azure 资源管理调用或具有资源 RBAC 访问权限的权限。 第二个应用访问仅在用户订阅中创建的 Azure 密钥保管库，用于无代理 VMware 迁移。 在无代理迁移中，Azure 迁移创建密钥保管库来管理订阅中复制存储帐户的访问密钥。 从设备启动发现时，它在 Azure 密钥保管库（客户租户中）具有 RBAC 访问权限。<br/><br/> **超V**-在载入期间。 Azure 迁移将创建一个 Azure AD 应用。 应用在设备代理和 Azure 迁移服务之间通信。 应用没有对 Azure 资源管理调用或具有资源 RBAC 访问权限的权限。 | 为[VMware、](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance)[超 V](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)或[物理服务器](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance)设置。
为 VMware 无代理迁移创建密钥保管库 | 要使用无代理 Azure 迁移服务器迁移 VMware VM，Azure 迁移将创建一个密钥保管库来管理订阅中复制存储帐户的访问密钥。 要创建保管库，请对 Azure 迁移项目所在的资源组设置权限（所有者或参与者和用户访问管理员）。 | [设置](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault)权限。

## <a name="supported-geographies"></a>支持的地理位置

可以在多个地理位置创建 Azure 迁移项目。 尽管只能在这些地理位置中创建项目，但可以评估或迁移其他目标位置的计算机。 项目地域仅用于存储已发现的元数据。

**地理** | **元数据存储位置**
--- | ---
Azure Government  | US Gov 弗吉尼亚州
亚太区 | 东亚或东南亚
澳大利亚 | 澳大利亚东部或澳大利亚东南部
巴西 | 巴西南部
Canada | 加拿大中部或加拿大东部
欧洲 | 欧洲北部或欧洲西部
法国 | 法国中部
印度 | 印度中部或南部
日本 |  日本东部或日本西部
韩国 | 韩国中部或韩国南部
United Kingdom | 英国南部或英国西部
United States | 美国中部或美国西部 2


 > [!NOTE]
 > 对 Azure 政府的支持目前仅适用于[旧版本的](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)Azure 迁移。



## <a name="vmware-assessment-and-migration"></a>VMware 评估和迁移

[查看](migrate-support-matrix-vmware.md)VMware VM 的 Azure 迁移服务器评估和服务器迁移支持矩阵。

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V 评估和迁移

[查看](migrate-support-matrix-hyper-v.md)超 V VM 的 Azure 迁移服务器评估和服务器迁移支持矩阵。



## <a name="azure-migrate-versions"></a>Azure Migrate 版本

有两个版本的 Azure Migrate 服务：

- **当前版本**：使用此版本可以创建新的 Azure 迁移项目、发现本地评估以及协调评估和迁移。 [了解详情](whats-new.md)。
- **以前的版本**：对于使用早期版本的 Azure 迁移的客户（仅支持本地 VMware VM 评估），现在应使用当前版本。 在前面版本中，无法创建新的 Azure 迁移项目或执行新的发现。

## <a name="next-steps"></a>后续步骤

- [评估 VMware VM](tutorial-assess-vmware.md)的迁移。
- [评估超 VM](tutorial-assess-hyper-v.md)的迁移。


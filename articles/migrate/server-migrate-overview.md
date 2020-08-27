---
title: 使用 Azure Migrate Server 迁移选择 VMware 迁移选项 |Microsoft Docs
description: 概述使用 Azure Migrate Server 迁移将 VMware Vm 迁移到 Azure 的选项
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: e62b9cea80f1ed7f672135b93e52ba606a717a6c
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950215"
---
# <a name="select-a-vmware-migration-option"></a>选择 VMware 迁移选项

可以使用 Azure Migrate 服务器迁移工具将 VMware VM 迁移到 Azure。 此工具提供多个选项用于迁移 VMware VM：

- 使用无代理复制进行迁移。 迁移 VM 且无需在其上安装任何组件。
- 使用复制代理进行迁移。 在要复制的 VM 上安装代理。


## <a name="compare-migration-methods"></a>比较迁移方法

使用这些选定的比较有助于确定要使用哪种方法。 你还可以查看 [无代理](migrate-support-matrix-vmware-migration.md#agentless-migration) 迁移和 [基于代理](migrate-support-matrix-vmware-migration.md#agent-based-migration) 的迁移的完全支持要求。

**设置** | **无代理** | **基于代理**
--- | --- | ---
**Azure 权限** | 你需要创建 Azure Migrate 项目的权限，并注册在部署 Azure Migrate 设备时创建 Azure AD 应用。 | 需要对 Azure 订阅具有参与者权限。 
**复制** | 最多可同时从 vCenter Server 复制300个 Vm。<br/> 如果迁移的 Vm 超过50个，请创建多个批处理 Vm。<br/> 一次复制更多会影响性能。<br/><br/> 在门户中，一次最多可选择 10 台计算机进行复制。 若要复制更多计算机，请分批添加，每次添加 10 台。| 复制容量通过扩展复制设备而增加。
**设备部署** | [Azure Migrate 设备](migrate-appliance.md)部署在本地。 | [Azure Migrate 复制设备](migrate-replication-appliance.md)部署在本地。
**Site Recovery 兼容性** | 兼容. | 如果已使用 Site Recovery 为计算机设置复制，则无法使用 Azure Migrate Server 迁移进行复制。
**目标磁盘** | 托管磁盘 | 托管磁盘
**磁盘限制** | OS 磁盘： 2 TB<br/><br/> 数据磁盘： 8 TB<br/><br/> 最大磁盘数：60 | OS 磁盘： 2 TB<br/><br/> 数据磁盘： 8 TB<br/><br/> 最大磁盘数：63
**传递磁盘** | 不支持 | 支持
**UEFI 启动** | 不支持 | Azure 中迁移的 VM 将自动转换为 BIOS 启动 VM。<br/><br/> OS 磁盘最多应有四个分区，卷应使用 NTFS 进行格式化。

## <a name="compare-deployment-steps"></a>比较部署步骤

查看限制后，了解部署每个解决方案所涉及的步骤可以帮助您决定选择哪个选项。

**任务** | **详细信息** |**无代理** | **基于代理**
--- | --- | --- | ---
**部署 Azure Migrate 设备** | 在 VMware VM 上运行的轻型设备。<br/><br/> 设备用于发现和评估计算机，并使用无代理迁移来迁移计算机。 | 必需。<br/><br/> 如果已设置设备进行评估，则可以使用同一设备进行无代理迁移。 | 不需要。<br/><br/> 如果已设置了要评估的设备，则可以将其保留在原位，如果已完成评估，则可以将其删除。
**使用服务器评估工具** | 利用 Azure Migrate：服务器评估工具评估计算机。 | 在迁移计算机之前，你可以对其进行评估，但不一定要这样做。 | 评估是可选的 | 评估是可选的。
**使用服务器迁移工具** | 在 Azure Migrate 项目中添加 Azure Migrate Server 迁移工具。 | 必需 | 必需
**为迁移准备 VMware** | 在 VMware 服务器和 Vm 上配置设置。 | 必需 | 必需
**在 Vm 上安装移动服务** | 移动服务在要复制的每个 VM 上运行 | 不是必需 | 必需
**部署复制设备** | [复制设备](migrate-replication-appliance.md)用于基于代理的迁移。 它在 Vm 上运行的移动服务与服务器迁移之间进行连接。 | 不是必需 | 必需
**复制 vm**。 启用 VM 复制。 | 配置复制设置并选择要复制的 Vm | 必需 | 必需
**运行测试迁移** | 运行测试迁移，确保一切按预期正常进行。 | 必需 | 必需
**运行完全迁移** | 迁移 Vm。 | 必需 | 必需



## <a name="next-steps"></a>后续步骤

将[VMware Vm 迁移](tutorial-migrate-vmware.md)到无代理迁移。




---
title: 使用 Azure Migrate Server 迁移选择 VMware 迁移选项 |Microsoft Docs
description: 概述使用 Azure Migrate Server 迁移将 VMware Vm 迁移到 Azure 的选项
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028710"
---
# <a name="select-a-vmware-migration-option"></a>选择 VMware 迁移选项

可以使用 Azure Migrate 服务器迁移工具将 VMware VM 迁移到 Azure。 此工具提供多个选项用于迁移 VMware VM：

- 使用无代理复制进行迁移。 迁移 VM 且无需在其上安装任何组件。
- 使用复制代理进行迁移。 在要复制的 VM 上安装代理。




## <a name="compare-migration-methods"></a>比较迁移方法

使用这些选定的比较有助于确定要使用哪种方法。 你还可以查看[无代理](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)迁移和[基于代理](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers)的迁移的完全支持要求。

**设置** | **款** | **基于代理**
--- | --- | ---
**Azure 权限** | 你需要创建 Azure Migrate 项目的权限，并注册在部署 Azure Migrate 设备时创建 Azure AD 应用。 | 需要对 Azure 订阅具有参与者权限。 
**同时复制** | 最多可同时从 vCenter Server 复制100个 Vm。<br/> 如果迁移的 Vm 超过50个，请创建多个批处理 Vm。<br/> 一次复制更多会影响性能。 | 不可用
**设备部署** | [Azure Migrate 设备](migrate-appliance.md)部署在本地。 | [Azure Migrate 复制设备](migrate-replication-appliance.md)部署在本地。
**Site Recovery 兼容性** | 兼容. | 如果已使用 Site Recovery 为计算机设置复制，则无法使用 Azure Migrate Server 迁移进行复制。
**目标磁盘** | 托管磁盘 | 托管磁盘
**磁盘限制** | OS 磁盘： 2 TB<br/><br/> 数据磁盘： 4 TB<br/><br/> 最大磁盘数：60 | OS 磁盘： 2 TB<br/><br/> 数据磁盘： 8 TB<br/><br/> 最大磁盘数：63
**传递磁盘** | 不支持 | 受支持
**UEFI 启动** | 不支持 | Azure 中迁移的 VM 将自动转换为 BIOS 启动 VM。<br/><br/> OS 磁盘最多应有四个分区，卷应使用 NTFS 进行格式化。


## <a name="deployment-steps-comparison"></a>部署步骤比较

查看限制后，了解部署每个解决方案所涉及的步骤可以帮助您决定选择哪个选项。

**任务** | **详细信息** |**款** | **基于代理**
--- | --- | --- | ---
**评估** | 迁移之前评估服务器。  评估是可选的。 建议你在迁移计算机之前对其进行评估，但不一定要这样做。 <br/><br/> 为了进行评估，Azure Migrate 会设置一个轻型设备，用于发现和评估 Vm。 | 如果在评估后运行无代理迁移，则为评估设置的同一个 Azure Migrate 设备用于无代理迁移。  |  如果在评估后运行基于代理的迁移，则不会在无代理迁移期间使用为评估设置的设备。 如果你不想执行进一步的发现和评估，则可以将设备置于适当位置，或将其删除。
**为迁移准备 VMware 服务器和 Vm** | 在 VMware 服务器和 Vm 上配置许多设置。 | 需要 | 需要
**添加服务器迁移工具** | 在 Azure Migrate 项目中添加 Azure Migrate Server 迁移工具。 | 需要 | 需要
**部署 Azure Migrate 设备** | 在 VMware VM 上设置一个轻型设备，以便进行 VM 发现和评估。 | 需要 | 非必需。
**在 Vm 上安装移动服务** | 在要复制的每个 VM 上安装移动服务 | 无需 | 需要
**部署 Azure Migrate 服务器迁移复制设备** | 在 VMware VM 上设置一个设备来发现 Vm，并在 Vm 上运行的移动服务与 Azure Migrate 服务器迁移之间建立桥梁 | 无需 | 需要
**复制 vm**。 启用 VM 复制。 | 配置复制设置并选择要复制的 Vm | 需要 | 需要
**运行测试迁移** | 运行测试迁移，确保一切按预期正常进行。 | 需要 | 需要
**运行完全迁移** | 迁移 Vm。 | 需要 | 需要




## <a name="next-steps"></a>后续步骤

将[VMware Vm 迁移](tutorial-migrate-vmware.md)到无代理迁移。




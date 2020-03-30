---
title: 使用 Azure 迁移服务器迁移选择 VMware 迁移选项 |微软文档
description: 提供使用 Azure 迁移服务器迁移将 VMware VM 迁移到 Azure 的选项概述
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028710"
---
# <a name="select-a-vmware-migration-option"></a>选择 VMware 迁移选项

可以使用 Azure Migrate 服务器迁移工具将 VMware VM 迁移到 Azure。 此工具提供多个选项用于迁移 VMware VM：

- 使用无代理复制进行迁移。 迁移 VM 且无需在其上安装任何组件。
- 使用复制代理进行迁移。 在要复制的 VM 上安装代理。




## <a name="compare-migration-methods"></a>比较迁移方法

使用这些选定的比较，以帮助您决定使用哪种方法。 您还可以查看[无代理](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)和[基于代理的](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers)迁移的完整支持要求。

**设置** | **无代理** | **基于代理**
--- | --- | ---
**Azure 权限** | 您需要权限才能创建 Azure 迁移项目，并注册在部署 Azure 迁移应用时创建的 Azure AD 应用。 | 需要 Azure 订阅上的参与者权限。 
**同时复制** | 最多可以从 vCenter 服务器复制 100 个 VM。<br/> 如果您有 50 多个 VM 进行迁移，请创建多批 VM。<br/> 一次复制更多将影响性能。 | NA
**设备部署** | [Azure 迁移设备](migrate-appliance.md)部署在本地。 | [Azure 迁移复制设备](migrate-replication-appliance.md)部署在本地。
**站点恢复兼容性** | 兼容。 | 如果已使用站点恢复为计算机设置复制设置复制，则无法使用 Azure 迁移服务器迁移进行复制。
**目标磁盘** | 托管磁盘 | 托管磁盘
**磁盘限制** | OS 磁盘： 2 TB<br/><br/> 数据磁盘： 4 TB<br/><br/> 最大磁盘数： 60 | OS 磁盘： 2 TB<br/><br/> 数据磁盘： 8 TB<br/><br/> 最大磁盘数： 63
**直通磁盘** | 不支持 | 支持
**UEFI 启动** | 不支持 | Azure 中迁移的 VM 将自动转换为 BIOS 引导 VM。<br/><br/> 操作系统磁盘应最多具有四个分区，并且卷应使用 NTFS 进行格式化。


## <a name="deployment-steps-comparison"></a>部署步骤比较

在查看限制后，了解部署每个解决方案所涉及的步骤可以帮助您决定选择哪个选项。

**任务** | **详细信息** |**无代理** | **基于代理**
--- | --- | --- | ---
**评估** | 在迁移之前评估服务器。  评估是可选的。 我们建议您在迁移机器之前对其进行评估，但不必评估它们。 <br/><br/> 为了进行评估，Azure 迁移设置一个轻量级设备来发现和评估 VM。 | 如果在评估后运行无代理迁移，则为评估设置的相同 Azure 迁移设备将用于无代理迁移。  |  如果在评估后运行基于代理的迁移，则在无代理迁移期间不使用为评估设置的设备。 如果不想执行进一步的发现和评估，您可以将产品保留到位，或者将其卸下。
**准备 VMware 服务器和 VM 进行迁移** | 在 VMware 服务器和 VM 上配置多个设置。 | 必选 | 必选
**添加服务器迁移工具** | 在 Azure 迁移项目中添加 Azure 迁移服务器迁移工具。 | 必选 | 必选
**部署 Azure 迁移设备** | 在 VMware VM 上设置轻量级设备，用于 VM 发现和评估。 | 必选 | 非必需。
**在 VM 上安装移动服务** | 在要复制的每个 VM 上安装移动服务 | 不是必需 | 必选
**部署 Azure 迁移服务器迁移复制设备** | 在 VMware VM 上设置设备以发现 VM，并在 VM 上运行的移动服务与 Azure 迁移服务器迁移之间架桥 | 不是必需 | 必选
**复制 VM**。 启用 VM 复制。 | 配置复制设置并选择要复制的 VM | 必选 | 必选
**运行测试迁移** | 运行测试迁移，确保一切按预期正常进行。 | 必选 | 必选
**运行完整迁移** | 迁移 VM。 | 必选 | 必选




## <a name="next-steps"></a>后续步骤

使用无代理迁移[迁移 VMware VM。](tutorial-migrate-vmware.md)




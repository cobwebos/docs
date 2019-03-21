---
title: 自动将大量 VM 迁移到 Azure | Microsoft Docs
description: 介绍如何借助脚本使用 Azure Site Recovery 迁移大量 VM
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 02/07/2019
ms.author: snehaa
ms.openlocfilehash: 74dabc49dd3d0e38f43dc758204c35ea1c0efd99
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438476"
---
# <a name="scale-migration-of-vms-using-azure-site-recovery"></a>使用 Azure Site Recovery 进行 VM 的规模化迁移

本文将帮助你理解借助脚本使用 Azure Site Recovery 迁移大量 VM 的过程。 可以在 GitHub 上的 [Azure PowerShell 示例](https://github.com/Azure/azure-docs-powershell-samples)存储库下载这些脚本。 这些脚本可用于将 VMware、AWS、GCP VM 和物理服务器迁移到 Azure。 如果将 VM 作为物理服务器迁移，也可以使用这些脚本迁移 Hyper-V VM。 这些脚本利用了[此处](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell)记录的 Azure Site Recovery PowerShell。

## <a name="current-limitations"></a>当前限制：
- 这些脚本目前仅支持迁移到非托管磁盘
- 仅支持迁移到标准磁盘
- 支持仅为目标 VM 的主 NIC 指定静态 IP 地址
- 这些脚本不接受与 Azure 混合权益相关的输入，你需要手动更新门户中复制的 VM 的属性

## <a name="how-does-it-work"></a>工作原理

### <a name="prerequisites"></a>必备组件
开始迁移前，需要执行以下步骤：
- 确保在 Azure 订阅中创建了 Site Recovery 保管库
- 确保配置服务器和进程服务器安装在源环境中，并且保管库可以发现环境
- 确保已创建复制策略并将其与配置服务器关联
- 确保已添加到配置服务器 （它将用于复制本地 Vm） 的 VM 管理员帐户
- 确保创建在 Azure 中的目标项目
    - 目标资源组
    - 目标存储帐户（及其资源组）
    - 用于故障转移的目标虚拟网络（及其资源组）
    - 目标子网
    - 用于测试故障转移的目标虚拟网络（及其资源组）
    - 可用性集（如果需要）
    - 目标网络安全组及其资源组
- 确保已确定目标 VM 的属性
    - 目标 VM 名称
    - Azure 中的目标 VM 大小（可以使用 Azure Migrate 评估来确定）
    - VM 中主 NIC 的专用 IP 地址
- 从 GitHub 上的 [Azure PowerShell 示例](https://github.com/Azure/azure-docs-powershell-samples)存储库下载脚本

### <a name="csv-input-file"></a>CSV 输入文件
完成所有先决条件后，需要创建一个 CSV 文件，其中包含要迁移的每台源计算机的数据。 输入 CSV 必须具有包含输入详细信息的标题行，以及包含需要迁移的每台计算机的详细信息的行。 所有脚本都设计为在同一 CSV 文件上工作。 脚本文件夹中提供了一个示例 CSV 模板以供参考。

### <a name="script-execution"></a>脚本执行
CSV 准备就绪后，可以通过以下步骤来执行本地 VM 的迁移：

**步骤编号** | **脚本名称** | **说明**
--- | --- | ---
第 | asr_startmigration.ps1 | 为 csv 中列出的所有 VM 启用复制，该脚本将创建一个 CSV 输出，其中包含每个 VM 的作业详细信息
2 | asr_replicationstatus.ps1 | 检查复制状态，该脚本会创建一个包含各 VM 的状态的 csv
3 | asr_updateproperties.ps1 | 复制/保护 VM 后，使用此脚本更新 VM 的目标属性（计算和网络属性）
4 | asr_propertiescheck.ps1 | 验证属性是否已正确更新
5 | asr_testmigration.ps1 |  启动 csv 中列出的 VM 的测试故障转移，该脚本将创建一个 CSV 输出，其中包含每个 VM 的作业详细信息
6 | asr_cleanuptestmigration.ps1 | 手动验证已执行测试故障转移的 VM 后，可以使用此脚本清理测试故障转移 VM
7 | asr_migration.ps1 | 对 csv 中列出的 VM 执行计划外的故障转移，该脚本将创建一个 CSV 输出，其中包含每个 VM 的作业详细信息。 该脚本会关闭本地 Vm 执行之前触发故障转移，应用程序一致性，建议您手动先关闭 Vm 执行该脚本之前。
8 | asr_completemigration.ps1 | 在 VM 上执行提交操作并删除 ASR 实体
9 | asr_postmigration.ps1 | 如果计划在故障转移后将网络安全组分配给 NIC，可以使用此脚本实现该操作。 它将 NSG 分配给目标 VM 中的任何一个 NIC。

## <a name="next-steps"></a>后续步骤

[详细了解](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure)如何使用 Azure Site Recovery 将服务器迁移到 Azure

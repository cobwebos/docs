---
title: 自动将大量 VM 迁移到 Azure | Microsoft Docs
description: 介绍如何借助脚本使用 Azure Site Recovery 迁移大量 VM
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: b45a158569b3be8250728293c1bf73c1a860a0f6
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67808024"
---
# <a name="scale-migration-of-vms-using-azure-site-recovery"></a>使用 Azure Site Recovery 进行 VM 的规模化迁移

本文可帮助你了解如何使用脚本迁移大量的 Vm 使用 Azure Site Recovery。 可以在 GitHub 上的 [Azure PowerShell 示例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery)存储库下载这些脚本。 这些脚本可用于将 VMware、 AWS、 GCP Vm 和物理服务器迁移到 Azure 中的托管磁盘。 如果将 VM 作为物理服务器迁移，也可以使用这些脚本迁移 Hyper-V VM。 利用 Azure Site Recovery PowerShell 脚本所述[此处](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell)。

## <a name="current-limitations"></a>当前限制：
- 支持仅为目标 VM 的主 NIC 指定静态 IP 地址
- 这些脚本不接受与 Azure 混合权益相关的输入，你需要手动更新门户中复制的 VM 的属性

## <a name="how-does-it-work"></a>工作原理

### <a name="prerequisites"></a>系统必备
开始迁移前，需要执行以下步骤：
- 确保在 Azure 订阅中创建了 Site Recovery 保管库
- 确保配置服务器和进程服务器安装在源环境中，并且保管库可以发现环境
- 确保已创建复制策略并将其与配置服务器关联
- 确保已添加到配置服务器 （它将用于复制本地 Vm） 的 VM 管理员帐户
- 确保创建在 Azure 中的目标项目
    - 目标资源组
    - 目标存储帐户 （和其资源组）-如果你打算迁移到高级托管磁盘创建高级存储帐户
    - 缓存存储帐户 （和其资源组）-在保管库所在的同一区域中创建标准存储帐户
    - 用于故障转移的目标虚拟网络（及其资源组）
    - 目标子网
    - 用于测试故障转移的目标虚拟网络（及其资源组）
    - 可用性集（如果需要）
    - 目标网络安全组及其资源组
- 确保已确定目标 VM 的属性
    - 目标 VM 名称
    - Azure 中的目标 VM 大小（可以使用 Azure Migrate 评估来确定）
    - VM 中主 NIC 的专用 IP 地址
- 从 GitHub 上的 [Azure PowerShell 示例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery)存储库下载脚本

### <a name="csv-input-file"></a>CSV 输入文件
已完成的所有先决条件后，您需要创建具有你想要迁移的每台源计算机的数据的 CSV 文件。 输入 CSV 必须具有包含输入详细信息的标题行，以及包含需要迁移的每台计算机的详细信息的行。 所有脚本都设计为在同一 CSV 文件上工作。 脚本文件夹中提供了一个示例 CSV 模板以供参考。

### <a name="script-execution"></a>脚本执行
CSV 准备就绪后，可以通过以下步骤来执行本地 VM 的迁移：

**步骤编号** | **脚本名称** | **说明**
--- | --- | ---
1 | asr_startmigration.ps1 | 为 csv 中列出的所有 VM 启用复制，该脚本将创建一个 CSV 输出，其中包含每个 VM 的作业详细信息
2 | asr_replicationstatus.ps1 | 检查复制状态，该脚本会创建一个包含各 VM 的状态的 csv
3 | asr_updateproperties.ps1 | 复制/保护 VM 后，使用此脚本更新 VM 的目标属性（计算和网络属性）
4 | asr_propertiescheck.ps1 | 验证属性是否已正确更新
5 | asr_testmigration.ps1 |  启动 csv 中列出的 VM 的测试故障转移，该脚本将创建一个 CSV 输出，其中包含每个 VM 的作业详细信息
6 | asr_cleanuptestmigration.ps1 | 手动验证已执行测试故障转移的 VM 后，可以使用此脚本清理测试故障转移 VM
7 | asr_migration.ps1 | 对 csv 中列出的 VM 执行计划外的故障转移，该脚本将创建一个 CSV 输出，其中包含每个 VM 的作业详细信息。 该脚本不会关闭本地虚拟机再触发故障转移的应用程序一致性，建议您手动先关闭 Vm 执行该脚本之前。
8 | asr_completemigration.ps1 | 执行提交操作在虚拟机上的和删除 Azure Site Recovery 实体
9 | asr_postmigration.ps1 | 如果计划在故障转移后将网络安全组分配给 NIC，可以使用此脚本实现该操作。 它分配到目标 VM 中的任意一个 NIC 的 NSG。

## <a name="how-to-migrate-to-managed-disks"></a>如何迁移到托管磁盘？
该脚本，默认情况下，迁移的 Vm 到 Azure 中的托管磁盘。 如果提供的目标存储帐户是高级存储帐户，高级托管磁盘创建 post 迁移。 缓存存储帐户仍可以是标准帐户。 如果目标存储帐户是标准存储帐户，标准磁盘创建迁移后。 

## <a name="next-steps"></a>后续步骤

[详细了解](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure)如何使用 Azure Site Recovery 将服务器迁移到 Azure

---
title: Microsoft Azure 备份服务器中的新增功能
description: Microsoft Azure 备份服务器提供用于保护 VM、文件和文件夹、工作负载等的增强备份功能。 了解如何安装或升级到 Azure 备份服务器 V3。
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 61430ce06d3e441fcfe0443eaaf5de3755b04624
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77582800"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Microsoft Azure 备份服务器中的新增功能

Microsoft Azure 备份 Server 版本3（MABS V3）是最新升级，其中包括关键 bug 修复、Windows Server 2019 支持、SQL 2017 支持以及其他功能和增强功能。 若要查看 MABS V3 中已修复的 bug 列表和安装说明，请参阅知识库文章 [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3)。

MABS V3 包含以下功能：

## <a name="volume-to-volume-migration"></a>卷到卷的迁移

发布 MABS V2 中的新式备份存储 (MBS) 时，我们宣布推出了工作负荷感知的存储，在其中可以根据存储属性，将某些工作负荷配置为备份到特定的存储。 但是，在配置后，你可能发现，需要将某些数据源的备份移到其他存储，以优化资源利用率。 MABS V3 使你可以通过[三个步骤](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842)迁移备份并将其配置为存储到不同的卷。

## <a name="prevent-unexpected-data-loss"></a>防止意外的数据丢失

在企业中，MABS 由管理员团队管理。 尽管用于备份的存储有指导原则可供参考，但在 MABS 中提供错误的卷作为备份存储可能会导致关键数据丢失。 在 MABS V3 中，可以使用[这些 PowerShell cmdlet](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage) 将这些卷配置为不可用于存储，来防止这种情况。

## <a name="custom-size-allocation"></a>自定义大小分配

新式备份存储 (MBS) 根据需要消耗少量的存储。 为此，在配置用于保护后，MABS 会计算备份数据的大小。 但是，如果要同时备份许多的文件和文件夹（例如，在文件服务器中备份时），大小计算可能会花费很长时间。 使用 MABS V3，可将 MABS 配置为接受默认卷大小而不是计算每个文件的大小，因而可以节省时间。

## <a name="optimized-cc-for-rct-vms"></a>针对 RCT VM 优化的一致性检查

MABS 使用 RCT（Hyper-V 中的本机更改跟踪），可以在发生 VM 崩溃等情况时减少非常耗时的一致性检查的需求。 与基于 VSS 快照的备份提供的更改跟踪相比，RCT 提供更好的复原能力。 在执行任何一致性检查期间，MABS V3 只会传输更改的数据，因而可以进一步优化网络和存储消耗。

## <a name="support-to-tls-12"></a>TLS 1.2 支持

TLS 1.2 是 Microsoft 建议的通信方式，并采用一流的加密。 MABS 现在支持 MABS 与受保护服务器之间的 TLS 1.2 通信，以进行基于证书的身份验证和云备份。

## <a name="vmware-vm-protection-support"></a>VMware VM 保护支持

现在，支持对生产部署使用 VMware VM 备份。 MABS V3 为 VMware VM 保护提供以下功能：

- 支持 vCenter 和 ESXi 6.5、5.5 和 6.0。
- 在云中自动保护 VMware VM。 如果将新的 VMware VM 添加到受保护的文件夹，则会在磁盘和云中自动保护这些 VM。
- VMware 备用位置恢复的恢复效率改进。

## <a name="sql-2017-support"></a>SQL 2017 支持

MABS V3 可与用作 MABS 数据库的 SQL 2017 一同安装。 可将 SQL Server 从 SQL 2016 升级到 SQL 2017，或全新安装 SQL Server。 还可以使用 MABS V3 在群集和非群集环境中备份 SQL 2017 工作负荷。

## <a name="windows-server-2019-support"></a>Windows Server 2019 支持

可在 Windows Server 2019 中安装 MABS V3。 若要将 MABS V3 与 WS2019 配合使用，可以在安装/升级到 MABS V3 之前将 OS 升级到 WS2019，或者在 WS2016 中安装/升级 V3 之后升级 OS。

MABS V3 是完整发行版，可直接安装在 Windows Server 2016、Windows Server 2019 中，或者从 MABS V2 升级。 在升级到或安装备份服务器 V3 之前，请阅读安装先决条件。
在[此处](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package)可以找到有关 MABS 安装/升级步骤的详细信息。

> [!NOTE]
>
> MABS 的代码基与 System Center Data Protection Manager 相同。 MABS v3 相当于 Data Protection Manager 1807。

## <a name="next-steps"></a>后续步骤

了解如何准备服务器或开始保护工作负载：

- [MABS V3 中的已知问题](backup-mabs-release-notes-v3.md)
- [准备备份服务器工作负载](backup-azure-microsoft-azure-backup.md)
- [使用备份服务器备份 VMware 服务器](backup-azure-backup-server-vmware.md)
- [使用备份服务器备份 SQL Server](backup-azure-sql-mabs.md)
- [将新式备份存储与备份服务器一起使用](backup-mabs-add-storage.md)

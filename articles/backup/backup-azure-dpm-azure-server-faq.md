---
title: Azure 备份服务器和 DPM 常见问题解答
description: 本文介绍有关 Microsoft Azure 备份服务器（MABS）和 DPM （Data Protection Manager）的常见问题的解答。
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/05/2019
ms.openlocfilehash: 35957a1e8a3d6c3d9be06d9d44dbcd47efa0e6ee
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173157"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure 备份服务器和 DPM - 常见问题解答

## <a name="general-questions"></a>一般问题

本文解答有关 Azure 备份服务器和 DPM 的常见问题。

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server"></a>是否可以使用 Azure 备份服务器为物理服务器创建裸机恢复 (BMR) 备份？

可以。

### <a name="can-i-register-the-server-to-multiple-vaults"></a>是否可以向多个保管库注册服务器？

不能。 一个 DPM 或 Azure 备份服务器只能注册到一个保管库。

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>可以使用 DPM 来备份 Azure Stack 中的应用吗？

不能。 可以使用 Azure 备份来保护 Azure Stack，但 Azure 备份不支持使用 DPM 来备份 Azure Stack 中的应用。

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>如果已经安装 Azure 备份代理来保护我的文件和文件夹，是否可以安装 System Center DPM 将本地工作负载备份到 Azure？

可以。 但应首先设置 DPM，然后再安装 Azure 备份代理。  按此顺序安装组件可以确保 Azure 备份代理能够与 DPM 一起工作。 不建议也不支持在安装 DPM 之前安装代理。

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>在安装 UR7 以及最新 Azure 备份代理之后，为何无法添加外部 DPM 服务器？

对于通过云对数据源进行保护的 DPM 服务器（使用 Update Rollup 7 之前的更新汇总），必须在安装 UR7 及最新 Azure 备份代理之后等待至少一天，然后才能开始“添加外部 DPM 服务器”。 需要一天的时间才能将 DPM 保护组的元数据上传到 Azure。 首次上传保护组元数据时通过一个每晚执行的作业实现。

## <a name="vmware-and-hyper-v-backup"></a>VMware 和 Hyper-V 备份

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>是否可以将 VMware vCenter 服务器备份到 Azure？

可以。 可以使用 Azure 备份服务器将 VMware vCenter Server 和 ESXi 主机备份到 Azure。

- [详细了解](backup-mabs-protection-matrix.md)支持的版本。
- [请按照下列步骤](backup-azure-backup-server-vmware.md)备份 VMware 服务器。

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>是否需要单独的许可证才能恢复完整的本地 VMware/Hyper-V 群集？

不需要单独的 VMware/Hyper-V 保护许可。

- 如果你是 System Center 客户，使用 System Center Data Protection Manager (DPM) 来保护 VMware VM。
- 如果不是 System Center 客户，可以使用 Azure 备份服务器（即用即付）来保护 VMware VM。

## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>如果使用 SQL AlwaysOn（使用磁盘上保护）配置了 SharePoint，我能否将 SharePoint 项恢复到原始位置？

可以，该项可以恢复到原始 SharePoint 站点。

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>如果使用 SQL AlwaysOn 配置了 SharePoint，我能否将 SharePoint 数据库恢复到原始位置？

由于 SharePoint 数据库是在 SQL AlwaysOn 中配置的，因此，除非删除可用性组，否则无法修改它们。 因此，DPM 无法将数据库还原到原始位置。 可以将 SQL Server 数据库恢复到另一个 SQL Server 实例。

## <a name="next-steps"></a>后续步骤

阅读其他常见问题：

- [详细了解](backup-support-matrix-mabs-dpm.md) Azure 备份服务器和 DPM 支持矩阵。
- [详细了解](backup-azure-mabs-troubleshoot.md) Azure 备份服务器和 DPM 故障排除准则。

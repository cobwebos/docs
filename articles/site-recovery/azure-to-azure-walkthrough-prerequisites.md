---
title: "开始将 Azure VM 复制到另一个区域之前 | Microsoft Docs"
description: "总结使用 Azure Site Recovery 服务在 Azure 区域之间复制 Azure VM 之前所要执行的步骤"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: d38fc766d5226be7161433555da9622e006c80e9
ms.contentlocale: zh-cn
ms.lasthandoff: 08/31/2017

---

# <a name="step-2-before-you-start"></a>步骤 2：开始之前

查看用于通过 [Azure Site Recovery](site-recovery-overview.md) 在 Azure 区域之间复制 Azure 虚拟机 (VM) 的[体系结构](azure-to-azure-walkthrough-architecture.md)之后，请参考本文检查先决条件。

- 完成本文后，应该对需要做好哪些准备才能正常完成部署有一个清楚的认识，并且会完成必备步骤。
- 请将任何评论发布到本文底部，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提问。

>[!NOTE]
>
> Azure VM 复制目前处于预览状态。



## <a name="support-recommendations"></a>支持建议

请查看下表。 在[支持矩阵](site-recovery-support-matrix-azure-to-azure.md)中获取支持要求的完整列表。

**组件** | **要求**
--- | ---
**恢复服务保管库** | 建议在用于灾难恢复的目标 Azure 区域中创建恢复服务保管库。 例如，若要将美国东部的源 VM 复制到美国中部，请在美国中部创建保管库。
**Azure 订阅** | 应启用 Azure 订阅，以便在用作灾难恢复区域的目标位置创建 VM。 请联系支持部门，启用所需配额。
**目标区域容量** | 在目标 Azure 区域，订阅应该为 VM、存储帐户和网络组件提供足够的容量。
**存储** | 使用源 Azure VM 的[存储指南](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)，以避免性能问题。<br/><br/> 存储帐户必须与保管库位于同一区域。<br/><br/> 不能复制到位于印度中部和印度南部的高级帐户。<br/><br/> 如果使用默认设置部署复制，Site Recovery 会基于源配置创建所需的存储帐户。 如果自定义设置，请遵循 [VM 磁盘的可伸缩性目标](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)。
**网络** | 需要允许特定的 URL/IP 范围从 Azure VM 建立出站连接。<br/><br/> 网络帐户必须与保管库位于同一区域。
**Azure VM** | 确保所有最新的根证书位于 Windows/Linux Azure VM 上。 否则，会出于安全约束的原因而无法在 Site Recovery 中注册 VM。
**Azure 用户帐户** | Azure 用户帐户需要具有某些[权限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能启用 Azure 虚拟机的复制。


## <a name="set-permissions-on-the-account"></a>设置帐户权限

1. 了解复制功能所需的[权限](site-recovery-role-based-linked-access-control.md)。
2. 遵照这些[说明](../active-directory/role-based-access-control-configure.md#add-access)添加权限。


## <a name="verify-target-resources"></a>验证目标资源

1. 启用 Azure 订阅，以便在用于灾难恢复、用作灾难恢复区域的目标区域中创建 VM。 请联系支持部门，启用所需配额。
2. 确保订阅中有足够的资源，能够启用大小与源 VM 匹配的 VM。 默认情况下，在设置复制时，Site Recovery 会为目标 VM 选择相同的大小或尽可能接近的大小。 详细了解如何对目标资源进行[故障排除](site-recovery-azure-to-azure-troubleshoot-errors.md#azure-resource-quota-issues-error-code-150097)。

## <a name="verify-azure-vm-certificates"></a>验证 Azure VM 证书

1. 检查所有最新的根证书是否在想要复制的 Windows 或 Linux VM 上。 如果没有最新的根证书，VM 会由于安全约束无法注册到 Site Recovery。
2. 对于 Windows VM，请执行以下操作：

    - 在 VM 上安装所有最新的 Windows 更新，使所有受信任的根证书保留在该计算机上。
    - 在未联网的环境中，请遵循组织中的标准 Windows 更新过程/证书更新过程，在 VM 上获取最新的根证书和更新的 CRL。
3. 对于 Linux VM，请遵循 Linux 分销商提供的指导，在 VM 上获取最新的受信任根证书和最新的证书撤销列表。 详细了解如何[排查](site-recovery-azure-to-azure-troubleshoot-errors.md#trusted-root-certificates-error-code-151066)受信任的根证书问题。


## <a name="next-steps"></a>后续步骤

转到[步骤 3：规划网络](azure-to-azure-walkthrough-network.md)设置出站连接。


---
title: "设置复制策略以使用 Azure Site Recovery 将 Hyper-V VM 复制到辅助站点 | Microsoft Docs"
description: "介绍如何设置策略，以使用 Azure Site Recovery 将 Hyper-V VM 复制到辅助 VMM 站点。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5d9b79cf-89f2-4af9-ac8e-3a32ad8c6c4d
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: a3ecc555e049914d7bffdddce7b0522f09362d24
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="step-8-set-up-a-replication-policy"></a>第 8 步：创建复制策略

配置[网络映射](vmm-to-vmm-walkthrough-network-mapping.md)后，根据本文设置复制策略，以使用 [Azure Site Recovery](site-recovery-overview.md) 将 Hyper-V 虚拟机 (VM) 复制到辅助站点。

阅读本文后，请在底部发布评论，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="before-you-start"></a>开始之前

- 创建复制策略后，使用该策略的所有主机都必须具有相同的操作系统。 VMM 云可包含运行不同 Windows Server 版本的 Hyper-V 主机，但此情况下需要多个复制策略。
- 可脱机执行初始复制。

## <a name="configure-replication-settings"></a>配置复制设置

1. 若要创建新的复制策略，请单击“准备基础结构” > “复制设置” > “+创建和关联”。

    ![网络](./media/vmm-to-vmm-walkthrough-replication/gs-replication.png)
2. 在“创建和关联策略”中指定策略名称。 源和目标类型应为 **Hyper-V**。
3. 在“Hyper-V 主机版本”中，选择主机上运行的操作系统。
4. 在“身份验证类型”和“身份验证端口”中，指定如何对主 Hyper-V 主机服务器和恢复 Hyper-V 主机服务器之间的流量进行身份验证。 除非有正常运行的 Kerberos 环境，否则请选择“证书”。 Azure Site Recovery 将为 HTTPS 身份验证自动配置证书。 无需手动执行任何操作。 默认情况下，端口 8083 和 8084（用于证书）在 Hyper-V 主机服务器上的 Windows 防火墙中将处于打开状态。 如果选择“Kerberos”，将使用 Kerberos 票证执行主机服务器的相互身份验证。 请注意，此设置仅适用于在 Windows Server 2012 R2 上运行的 Hyper-V 主机服务器。
5. 在“复制频率” 中，指定要在初始复制后复制增量数据的频率（每隔 30 秒、5 或 15 分钟）。
6. 在“恢复点保留期”中，针对每个恢复点指定保留期的时长（以小时为单位）。 受保护的计算机可以恢复到某个时段内的任意时间点。
7. 在“应用一致性快照频率”中，指定创建包含应用程序一致性快照的恢复点的频率（1-12 小时）。 Hyper-V 使用两种类型的快照 — 标准快照，它提供整个虚拟机的增量快照；与应用程序一致的快照，它生成虚拟机内的应用程序数据的时间点快照。 与应用程序一致的快照使用卷影复制服务 (VSS) 来确保应用程序在拍摄快照时处于一致状态。 如果启用了与应用程序一致的快照，它将影响在源虚拟机上运行的应用程序的性能。 请确保设置的值小于配置的额外恢复点的数目。
8. 在“数据传输压缩”中，指定是否应压缩所传输的复制数据。
9. 选择“删除副本 VM”，指定禁用对源 VM 的保护时应删除副本虚拟机。 如果启用此设置，禁用对源 VM 的保护时，将从站点恢复控制台中删除该 VM、在 VMM 控制台中删除 VMM 的站点恢复设置，并删除副本。
10. 如果要通过网络进行复制，请在“初始复制方法”中指定是要启动还是计划初始复制。 要节省网络带宽，可以将它计划在非高峰时间运行。 然后单击“确定”。

     ![复制策略](./media/vmm-to-vmm-walkthrough-replication/gs-replication2.png)
11. 当创建新策略时，该策略自动与 VMM 云关联。 在“复制策略”中单击“确定”。 可以在“复制”“策略名称”>“关联 VMM 云”中，将其他 VMM 云（及其中的 VM）与此复制策略相关联。

     ![复制策略](./media/vmm-to-vmm-walkthrough-replication/policy-associate.png)



## <a name="prepare-for-offline-initial-replication"></a>为脱机初始复制做准备

可以使用脱机复制来执行初始数据复制。 可按如下所述做好准备：

* 在源服务器上，需要指定从中进行数据导出的路径位置。 在导出路径上分配对 NTFS 的“完全控制”权限和对 VMM 服务的“共享”权限。 在目标服务器上，指定要从中导入数据的路径位置。 在此路径上分配同样的权限。
* 如果导入或导出路径是共享的，请为共享路径所在的远程计算机上的 VMM 服务帐户分配 Administrator、Power User、Print Operator 或 Server Operator 组成员资格。
* 如果使用任何运行方式帐户来添加主机，请在 VMM 中在导入和导出路径上为运行方式帐户分配读取和写入权限。
* 导入和导出共享不应当位于用作 Hyper-V 主机服务器的任何计算机上，因为 Hyper-V 不支持环回配置。
* 在 Active Directory 中，在包含要保护的虚拟机的每个 Hyper-V 主机服务器上，启用并配置约束委派以信任导入和导出路径所在的远程计算机，如下所述：
  1. 在域控制器上，打开“Active Directory 用户和计算机”。
  2. 在控制台树中，单击“域名” > “计算机”。
  3. 右键单击 Hyper-V 主机服务器名称 >“属性”。
  4. 在“委托”选项卡上，单击“仅信任此计算机来委派指定的服务”。
  5. 单击“使用任意身份验证协议”。
  6. 单击“添加” > “用户和计算机”。
  7. 键入托管导入路径的计算机的名称，并单击“确定”。 在可用服务列表中，按住 CTRL 键的同时单击“cifs” >  单击“确定”。 针对承载着导入路径的主机的名称重复该步骤。 根据需要针对其他 Hyper-V 主机服务器重复该步骤。



## <a name="next-steps"></a>后续步骤

转到[步骤 9：启用复制](vmm-to-vmm-walkthrough-enable-replication.md)。

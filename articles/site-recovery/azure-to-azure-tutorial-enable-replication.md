---
title: "使用 Azure Site Recovery（预览版）为 Azure VM 设置到辅助 Azure 区域的灾难恢复"
description: "了解如何使用 Azure Site Recovery 服务为 Azure VM 设置到其他 Azure 区域的灾难恢复。"
services: site-recovery
author: rajani-janaki-ram
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/19/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: c9cb2bfe459421ee6e28b9d83dffc569b7592c1f
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region-preview"></a>为 Azure VM 设置到辅助 Azure 区域的灾难恢复（预览版）

[Azure Site Recovery](site-recovery-overview.md) 服务有助于实施灾难恢复策略，因为它可以管理和协调本地计算机和 Azure 虚拟机 (VM) 的复制、故障转移和故障回复。

本教程演示了如何为 Azure VM 设置到辅助 Azure 区域的灾难恢复。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建恢复服务保管库
> * 验证目标资源设置
> * 为 VM 设置出站访问
> * 为虚拟机启用复制

## <a name="prerequisites"></a>先决条件

完成本教程：

- 请确保了解[方案体系结构和组件](concepts-azure-to-azure-architecture.md)。
- 查看所有组件的[支持要求](site-recovery-support-matrix-azure-to-azure.md)。

## <a name="create-a-vault"></a>创建保管库

在除了源区域之外的任意区域中创建保管库。

1. 登录到 [Azure 门户](https://portal.azure.com) > **恢复服务**。
2. 单击“新建” > “监视和管理” > “备份和 Site Recovery”。
3. 在“名称”中，指定一个友好名称以标识该保管库。 如果有多个订阅，请选择合适的一个。
4. 创建一个资源组或选择一个现有的资源组。 指定 Azure 区域。 若要查看受支持的区域，请参阅 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)中的“地域可用性”。
5. 若要从仪表板快速访问保管库，请单击“固定到仪表板”，然后单击“创建”。

   ![新保管库](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   新保管库将添加到“仪表板”中的“所有资源”下，以及“恢复服务保管库”主页面上。

## <a name="verify-target-resources"></a>验证目标资源

1. 验证 Azure 订阅是否允许在用于灾难恢复的目标区域中创建 VM。 请联系支持部门，启用所需配额。

2. 确保订阅中有足够的资源，能够支持大小与源 VM 匹配的 VM。 Site Recovery 会为目标 VM 选择相同的大小或尽可能接近的大小。

## <a name="configure-outbound-network-connectivity"></a>配置出站网络连接

要使 Site Recovery 按预期工作，需在希望复制的 VM 中对出站网络连接进行一些更改。

- Site Recovery 不支持使用身份验证代理来控制网络连接。
- 如果使用身份验证代理，则无法启用复制。

### <a name="outbound-connectivity-for-urls"></a>URL 的出站连接

如果使用基于 URL 的防火墙代理来控制出站连接，请允许访问 Site Recovery 使用的以下 URL。

| **URL** | **详细信息** |
| ------- | ----------- |
| *.blob.core.windows.net | 允许将数据从 VM 写入源区域中的缓存存储帐户。 |
| login.microsoftonline.com | 向 Site Recovery 服务 URL 提供授权和身份验证。 |
| *.hypervrecoverymanager.windowsazure.com | 允许 VM 与 Site Recovery 服务进行通信。 |
| *.servicebus.windows.net | 允许 VM 写入 Site Recovery 监视和诊断数据。 |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 地址范围的出站连接

当使用任何基于 IP 的防火墙、 代理或 NSG 规则来控制出站连接时，需要将以下 IP 地址范围列入白名单。 从以下链接下载范围列表：

  - [Microsoft Azure 数据中心 IP 范围](http://www.microsoft.com/en-us/download/details.aspx?id=41653)
  - [德国的 Windows Azure 数据中心 IP 范围](http://www.microsoft.com/en-us/download/details.aspx?id=54770)
  - [中国的 Windows Azure 数据中心 IP 范围](http://www.microsoft.com/en-us/download/details.aspx?id=42064)
  - [Office 365 URL 和 IP 地址范围](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Site Recovery 服务终结点 IP 地址](https://aka.ms/site-recovery-public-ips)

使用这些列表配置网络中的网络访问控制。 可以使用此[脚本](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702)创建所需的 NSG 规则。

## <a name="verify-azure-vm-certificates"></a>验证 Azure VM 证书

检查所有最新的根证书是否在想要复制的 Windows 或 Linux VM 上。 如果没有最新的根证书，则 VM 会由于安全约束而无法注册到 Site Recovery。

- 对于 Windows VM，请在 VM 上安装所有最新的 Windows 更新，使所有受信任的根证书位于该计算机上。 在未联网的环境中，请按照你的组织的标准 Windows 更新和证书更新过程执行操作。

- 对于 Linux VM，请遵循 Linux 分销商提供的指导，在 VM 上获取最新的受信任根证书和证书吊销列表。

## <a name="set-permissions-on-the-account"></a>设置帐户权限

Azure Site Recovery 提供了三个用于控制 Site Recovery 管理操作的内置角色。

- **Site Recovery 参与者** - 此角色拥有管理恢复服务保管库中 Azure Site Recovery 操作所需的全部权限。 不过，拥有此角色的用户既无法创建或删除恢复服务保管库，也无法向其他用户分配访问权限。 此角色最适合分配给灾难恢复管理员，这样他们就可以为应用程序或整个组织启用和管理灾难恢复。

- **Site Recovery 操作员** - 此角色有权执行和管理故障转移和故障回复操作。 拥有此角色的用户无法启用或禁用复制、无法创建或删除保管库，也无法注册新的基础结构或向其他用户分配访问权限。 此角色最适合分配给灾难恢复操作员，这样他们就可以遵循应用程序所有者或 IT 管理员的指示，对虚拟机或应用程序进行故障转移。 解决灾难后，DR 操作员可以对虚拟机进行重新保护和故障回复。

- **Site Recovery 读者** - 此角色有权查看所有 Site Recovery 管理操作。 此角色最适合分配给 IT 监视主管，这样他们就可以监视当前保护状态并创建支持票证。

详细了解 [Azure RBAC 内置角色](../active-directory/role-based-access-built-in-roles.md)

## <a name="enable-replication"></a>启用复制

### <a name="select-the-source"></a>选择源

1. 在“恢复服务保管库”中，单击保管库名称 >“+复制”。
2. 在“源”中，选择“Azure - 预览版”。
3. 在“源位置”中，选择当前运行 VM 的 Azure 源区域。
4. 为 VM 选择 Azure 虚拟机部署模型：“资源管理器”或“经典”。
5. 为资源管理器 VM 选择“源资源组”，为经典 VM 选择“云服务”。
6. 单击“确定”保存设置。

### <a name="select-the-vms"></a>选择 VM

Site Recovery 检索与订阅和资源组/云服务关联的 VM 列表。

1. 在“虚拟机”中，选择要复制的 VM。
2. 单击 **“确定”**。

### <a name="configure-replication-settings"></a>配置复制设置

Site Recovery 会针对目标区域创建默认设置和复制策略。 你可以根据需要更改设置。

1. 单击“设置”查看目标设置。
2. 若要替代默认目标设置，请单击“自定义”。 

![配置设置](./media/azure-to-azure-tutorial-enable-replication/settings.png)


- **目标位置**：用于灾难恢复的目标区域。 建议选择与 Site Recovery 保管库位置匹配的目标位置。

- **目标资源组**：故障转移后，目标区域中用于容纳 Azure VM 的资源组。 默认情况下，Site Recovery 会在目标位置中创建一个带有“asr”后缀的新资源组。

- **目标虚拟网络**：故障转移后，目标区域中 VM 所位于的网络。
  默认情况下，Site Recovery 会在目标位置中创建一个带有“asr”后缀的新虚拟网络（以及子网）。

- **缓存存储帐户**：Site Recovery 使用源区域中的一个存储帐户。 复制到目标位置之前，对源 VM 的更改将发送到此帐户。

- **目标存储帐户**：默认情况下，Site Recovery 会在目标区域中创建新存储帐户，从而形成源 VM 存储帐户的镜像。

- **目标可用性集**：默认情况下，Site Recovery 会在目标区域中创建一个带有“asr”后缀的新可用性集。 如果 VM 是源区域中一个集的一部分，则仅可添加可用性集。

- **复制策略名称**：策略名称。

- **恢复点保留期**： 默认情况下，Site Recovery 会将恢复点保留 24 小时。 可将此值配置为 1 - 72 小时。

- **“应用一致”快照频率**：默认情况下，Site Recovery 每隔 4 小时拍摄 1 次“应用一致”快照。 可将此值配置为 1 - 12 小时之间的任何值。 “应用一致”快照是 VM 内应用程序数据的时间点快照。 卷影复制服务 (VSS) 确保 VM 上的应用在拍摄快照时处于一致状态。

### <a name="track-replication-status"></a>跟踪复制状态

1. 在“设置”中，单击“刷新”以获取最新状态。

2. 可以在“设置”>“作业”>“Site Recovery 作业”中，跟踪“启用保护”作业的进度。

3. 在“设置” > “复制的项”中，可以查看 VM 的状态和初始复制进度。 单击 VM，向下钻取其设置。

## <a name="next-steps"></a>后续步骤

在本教程中，已经为 Azure VM 配置了灾难恢复。 下一步骤是对配置进行测试。

> [!div class="nextstepaction"]
> [运行灾难恢复演练](azure-to-azure-tutorial-dr-drill.md)

---
title: "使用 Azure Site Recovery 针对物理本地服务器设置到 Azure 的灾难恢复 | Microsoft Docs"
description: "了解如何使用 Azure Site Recovery 服务针对本地 Windows 和 Linux 服务器设置到 Azure 的灾难恢复。"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/08/2018
ms.author: raynew
ms.openlocfilehash: d460da197c6e9f0bface402d83d4788f8164cc9c
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>针对本地物理服务器设置到 Azure 的灾难恢复

[Azure Site Recovery](site-recovery-overview.md) 服务可管理和协调本地计算机和 Azure 虚拟机 (VM) 的复制、故障转移和故障回复，进而有利于灾难恢复策略。

本教程演示如何对本地物理 Windows 和 Linux 服务器设置到 Azure 的灾难恢复。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置 Azure 和本地先决条件
> * 为 Site Recovery 创建恢复服务保管库 
> * 设置源和目标复制环境
> * 创建复制策略
> * 为服务器启用复制

## <a name="prerequisites"></a>先决条件

完成本教程：

- 请确保了解[方案体系结构和组件](physical-azure-architecture.md)。
- 查看所有组件的[支持要求](vmware-physical-secondary-support-matrix.md)。
- 请确保想要复制的服务器符合 [Azure VM 要求](vmware-physical-secondary-support-matrix.md#replicated-vm-support)。
- 准备 Azure。 需要 Azure 订阅、Azure 虚拟网络和存储帐户。
- 准备一个帐户用于在要复制的每个服务器上自动安装移动服务。

> [!NOTE]
> 在开始之前，请注意，故障转移到 Azure 后，物理服务器将不能故障回复到本地物理计算机。 只能故障回复到 VMware VM。 


### <a name="set-up-an-azure-account"></a>设置 Azure 帐户

获取 Microsoft [Azure 帐户](http://azure.microsoft.com/)。

- 可以从 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。
- 了解 [Site Recovery 定价](site-recovery-faq.md#pricing)，并获取[定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)。
- 了解 Site Recovery [支持的区域](https://azure.microsoft.com/pricing/details/site-recovery/)。

### <a name="verify-azure-account-permissions"></a>验证 Azure 帐户权限

请确保 Azure 帐户具有将 VM 复制到 Azure 的权限。

- 查看将计算机复制到 Azure 所需的[权限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)。
- 验证和修改[基于角色的访问](../active-directory/role-based-access-control-configure.md)权限。 



### <a name="set-up-an-azure-network"></a>设置 Azure 网络

设置 [Azure 网络](../virtual-network/quick-create-portal.md)。

- 在故障转移后创建 Azure VM 时，Azure VM 将置于此网络中。
- 该网络应位于与恢复服务保管库相同的区域


## <a name="set-up-an-azure-storage-account"></a>设置 Azure 存储帐户

设置 [Azure 存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)。

- Site Recovery 将本地计算机复制到 Azure 存储。 发生故障转移后，将从存储中创建 Azure VM。
- 存储帐户必须位于与恢复服务保管库相同的区域。
- 存储帐户可以是标准帐户，也可以是[高级](../virtual-machines/windows/premium-storage.md)帐户。
- 如果创建的是高级帐户，还需要额外使用标准帐户来记录数据。



### <a name="prepare-an-account-for-mobility-service-installation"></a>准备一个帐户用于安装移动服务

必须在要复制的每个服务器上安装 Mobility Service。 为服务器启用复制后，Site Recovery 会自动安装此服务。 若要自动安装，需要准备一个由 Site Recovery 用于访问服务器的帐户。

- 可以使用域或本地帐户
- 对于 Windows VM，如果使用的不是域帐户，则在本地计算机上禁用远程用户访问控制。 为此，请在注册表中的 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 下添加值为 1 的 DWORD 项 **LocalAccountTokenFilterPolicy**。
- 若要添加注册表项以禁用 CLI 中的设置，请键入：``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- 对于 Linux，该帐户应是源 Linux 服务器上的根。


## <a name="create-a-vault"></a>创建保管库

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>选择保护目标

选择要复制的内容以及要将内容复制到的位置。

1. 单击“恢复服务保管库”> 保管库。
2. 在“资源”菜单中，单击“Site Recovery” > “准备基础结构” > “保护目标”。
3. 在“保护目标”中，选择“到 Azure” > “未虚拟化/其他”。

## <a name="set-up-the-source-environment"></a>设置源环境

设置配置服务器，将它注册到保管库中，并且发现 VM。

1. 单击“Site Recovery” > “准备基础结构” > “源”。
2. 如果没有配置服务器，请单击“+ 配置服务器”。
3. 在“添加服务器”中，检查“配置服务器”是否已显示在“服务器类型”中。
4. 下载站点恢复统一安装程序安装文件。
5. 下载保管库注册密钥。 运行统一安装程序时需要用到此密钥。 生成的密钥有效期为 5 天。

   ![设置源](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>在保管库中注册配置服务器

开始之前，请执行以下操作： 

- 在配置服务器计算机上，确保将系统时钟与[时间服务器](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)进行同步。 它应与之匹配。 如果它提前或落后 15 分钟，安装程序可能会失败。
- 请确保计算机可访问这些 URL：[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]

- 基于 IP 地址的防火墙规则应允许与 Azure 通信。
- 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。
- 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围（用于访问控制和标识管理）。

以本地管理员身份运行统一安装程序，安装配置服务器。 进程服务器和主目标服务器也默认安装在配置服务器上。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

注册完成后，配置服务器会显示在保管库的“设置” > “服务器”页中。


## <a name="set-up-the-target-environment"></a>设置目标环境

选择并验证目标资源。

1. 单击“准备基础结构” > “目标”，并选择要使用的 Azure 订阅。
2. 指定目标部署模型。
3. Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。

   ![目标](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>创建复制策略

1. 若要创建新的复制策略，请单击“Site Recovery 基础结构” > “复制策略” > “+ 复制策略”。
2. 在“创建复制策略”中指定策略名称。
3. 在“RPO 阈值”中，指定恢复点目标 (RPO) 限制。 此值指定创建数据恢复点的频率。 如果连续复制超出此限制，将生成警报。
4. 在“恢复点保留期”中，指定每个恢复点的保留期时长（以小时为单位）。 可以将复制的虚拟机恢复到窗口中的任何点。 复制到高级存储的计算机最多支持 24 小时的保留期，复制到标准存储的计算机最多支持 72 小时的保留期。
5. 在“应用一致性快照频率”中，指定创建包含应用程序一致性快照的恢复点的频率（以分钟为单位）。 单击“确定”创建该策略。

    ![复制策略](./media/physical-azure-disaster-recovery/replication-policy.png)


此策略自动与配置服务器关联。 默认情况下会自动创建一个匹配策略以用于故障回复。 例如，如果复制策略是 rep-policy，则创建故障回复策略 rep-policy-failback。 从 Azure 启动故障回复之前，不会使用此策略。

## <a name="enable-replication"></a>启用复制

为每个服务器启用复制。

- 启用复制后，Site Recovery 会安装移动服务。
- 为服务器启用复制后，可能要等 15 分钟或更长时间，更改才会生效并显示在门户中。

1. 单击“复制应用程序” > “源”。
2. 在“源”中选择配置服务器。
3. 在“计算机类型”中，选择“物理计算机”。
4. 选择进程服务器（配置服务器）。 然后单击“确定”。
5. 在“目标”中，选择故障转移后要在其中创建 Azure VM 的订阅和资源组。 选择要在 Azure 中使用的部署模型（经典或资源管理）。
6. 选择要用于复制数据的 Azure 存储帐户。 
7. 选择 Azure VM 在故障转移后创建时所要连接的 Azure 网络和子网。
8. 选择“立即为选定的计算机配置”，将网络设置应用到选择保护的所有计算机。 选择“稍后配置”以选择每个计算机的 Azure 网络。 
9. 在“物理计算机”中，单击“+物理计算机”。 指定名称和 IP 地址。 选择要复制的计算机的操作系统。 发现和列出服务器需要几分钟的时间。 
10. 在“属性” > “配置属性”中，选择进程服务器在计算机上自动安装移动服务时使用的帐户。
11. 在“复制设置” > “配置复制设置”中，检查是否选择了正确的复制策略。 
12. 单击“启用复制”。 可以在“设置” > “作业” > “Site Recovery 作业”中，跟踪“启用保护”作业的进度。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。


若要监视添加的服务器，可在“配置服务器” > “上次联系位置”查看上次发现服务器的时间。 若要添加计算机而不想要等待计划的发现时间，请突出显示配置服务器（不要单击它），并单击“刷新”。

## <a name="next-steps"></a>后续步骤

[运行灾难恢复演练](tutorial-dr-drill-azure.md)。

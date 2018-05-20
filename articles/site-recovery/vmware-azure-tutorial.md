---
title: 使用 Azure Site Recovery 针对本地 VMware VM 设置到 Azure 的灾难恢复 | Microsoft 文档
description: 了解如何使用 Azure Site Recovery 针对本地 VMware VM 设置到 Azure 的灾难恢复。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 173e31e3b1f855d488f7f8baf6659b1521ea7aa5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>针对本地 VMware VM 设置到 Azure 的灾难恢复

本教程演示如何针对运行 Windows 的本地 VMware VM 设置到 Azure 的灾难恢复。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 输入复制源和目标。
> * 设置源复制坏境，包括本地 Azure Site Recovery 组件和目标复制环境。
> * 创建复制策略。
> * 为 VM 启用复制。

本教程为系列教程中的第三个教程。 本教程假设你已完成前面教程中的以下任务：

* [准备 Azure](tutorial-prepare-azure.md)。 本教程介绍如何设置 Azure 存储帐户和网络、确保 Azure 帐户具有适当的权限，以及如何创建恢复服务保管库。
* [准备本地 VMware](vmware-azure-tutorial-prepare-on-premises.md)。 在本教程中将准备帐户，以便 Site Recovery 可以访问 VMware 服务器以发现 VM，并在为 VM 启用复制时，可以选择执行 Site Recovery 移动服务组件的推送安装。 还需确保 VMware 服务器和 VM 符合 Site Recovery 要求。

在开始之前，[查看灾难恢复方案的体系结构](vmware-azure-architecture.md)会有所帮助。


## <a name="select-a-replication-goal"></a>选择复制目标

1. 在“恢复服务保管库”中，选择保管库名称“ContosoVMVault”。
2. 在“入门”中，选择“Site Recovery”， 然后选择“准备基础结构”。
3. 在“保护目标” > “计算机所在位置”中，选择“本地”。
4. 在“要将计算机复制到何处?”中，选择“复制到 Azure”。
5. 在“你的计算机是否已虚拟化”中，选择“是，带有 VMware vSphere 虚拟机监控程序”。 然后选择“确定”。

## <a name="set-up-the-source-environment"></a>设置源环境


若要设置源环境，需要部署一台高度可用的本地计算机来托管本地 Site Recovery 组件。 组件包括配置服务器、进程服务器和主目标服务器：

- 配置服务器在本地和 Azure 之间协调通信并管理数据复制。
- 进程服务器充当复制网关。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将其发送到 Azure 存储。 进程服务器还会将移动服务安装在要复制的 VM 上，并执行针对本地 VMware VM 的自动发现。
- 主目标服务器处理从 Azure 进行故障回复期间产生的复制数据。

若要将配置服务器设置为高度可用的 VMware VM，请下载一个已准备好的开放虚拟化格式 (OVF) 模板，并将该模板导入 VMware 以创建 VM。 在设置配置服务器以后，请将它注册到保管库中。 注册后，Site Recovery 可发现本地 VMware VM。

> [!TIP]
> 本教程使用 OVF 模板创建配置服务器 VMware VM。 如果无法执行此操作，可以运行[手动设置](physical-manage-configuration-server.md)来执行此操作。 


### <a name="download-the-vm-template"></a>下载 VM 模板

1. 在保管库中，转到“准备基础结构” > “源”。
2. 在“准备源”中，选择“+配置服务器”。
3. 在“添加服务器”中，检查“VMware 的配置服务器”是否已显示在“服务器类型”中。
4. 下载配置服务器的 OVF 模板。

  > [!TIP]
  可以直接从 [Microsoft 下载中心](https://aka.ms/asrconfigurationserver)下载最新版本的配置服务器模板。

## <a name="import-the-template-in-vmware"></a>在 VMware 中导入模板

1. 使用 VMWare vSphere 客户端登录到 VMware vCenter 服务器或 vSphere ESXi 主机。
2. 在“文件”菜单中，选择“部署 OVF 模板”以启动“部署 OVF 模板”向导。 

     ![OVF 模板](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. 在“选择源”中，输入下载的 OVF 所在的位置。
4. 在“查看详细信息”中，选择“下一步”。
5. 在“选择名称和文件夹”和“选择配置”中，接受默认设置。
6. 在“选择存储”中，为获得最佳性能，请在“选择虚拟磁盘格式”中选择“Thick Provision Eager Zeroed”。
7. 在余下的向导页中，接受默认设置。
8. 在“准备完成”中：

    * 若要使用默认设置来设置 VM，请选择“部署后打开” > “完成”。

    * 若要添加其他网络接口，请清除“部署后打开”， 然后选择“完成”。 默认情况下，配置服务器模板是使用单个 NIC 部署的。 可以在部署后添加其他 NIC。

## <a name="add-an-additional-adapter"></a>添加其他适配器

若要将其他 NIC 添加到配置服务器，请在将服务器注册到保管库中之前添加。 注册后不支持添加其他适配器。

1. 在 vSphere 客户端库存中，右键单击 VM 并选择“编辑设置”。
2. 在“硬件”中，选择“添加” > “以太网适配器”。 然后，选择“下一步”。
3. 选择适配器类型和网络。 
4. 若要在打开 VM 时连接虚拟 NIC，请选择“打开时连接”。 选择“下一步” > “完成”。 然后选择“确定”。


## <a name="register-the-configuration-server"></a>注册配置服务器 

1. 通过 VMWare vSphere 客户端控制台打开 VM。
2. VM 将启动并进入 Windows Server 2016 安装体验。 接受许可协议，然后输入管理员密码。
3. 安装完成后，以管理员身份登录到 VM。
4. 首次登录时，会启动 Azure Site Recovery 配置工具。
5. 输入用于向 Site Recovery 注册配置服务器的名称。 然后，选择“下一步”。
6. 该工具会检查 VM 是否能够连接到 Azure。 建立连接后，选择“登录”以登录到 Azure 订阅。 使用的凭据必须有权访问配置服务器所要注册到的保管库。
7. 该工具将执行一些配置任务，然后重新启动。
8. 再次登录到计算机。 配置服务器管理向导会自动启动。

### <a name="configure-settings-and-add-the-vmware-server"></a>配置设置并添加 VMware 服务器

1. 在配置服务器管理向导中选择“设置连接”，然后选择进程服务器用于接收来自 VM 的复制流量的 NIC。 再选择“保存”。 配置后无法更改此设置。
2. 在“选择恢复服务保管库”中，选择自己的 Azure 订阅以及相关的资源组和保管库。
3. 在“安装第三方软件”中，接受许可协议。 选择“下载并安装”，安装 MySQL 服务器。
4. 选择“安装 VMware PowerCLI”。 执行此操作之前，请确保所有浏览器窗口已关闭。 然后选择“继续”。
5. 在“验证设备配置”中验证先决条件，然后继续。
6. 在“配置 vCenter Server/vSphere ESXi 服务器”中，输入要复制的 VM 所在的 vCenter Server 或 vSphere 主机的 FQDN 或 IP 地址。 输入服务器侦听的端口。 为保管库中的 VMware 服务器输入一个可用的友好名称。
7. 输入配置服务器用来连接到 VMware 服务器的凭据。 Site Recovery 将使用这些凭据自动发现可复制的 VMware VM。 选择“添加”，然后选择“继续”。
8. 在“配置虚拟机凭据”中，输入用于在计算机上自动安装移动服务的用户名和密码（如果已启用复制）。 对于 Windows 计算机，该帐户在要复制的计算机上需有本地管理员特权。 对于 Linux，请提供根帐户的详细信息。
9. 选择“完成配置”以完成注册。 
10. 注册完成后，请在 Azure 门户中确认配置服务器和 VMware 服务器是否已在保管库中的“源”页上列出。 然后，选择“确定”以配置目标设置。


Site Recovery 会使用指定的设置连接到 VMware 服务器，并且会发现 VM。

> [!NOTE]
> 帐户名出现在门户中可能需要 15 分钟或更长时间。 若要立即更新，请选择“配置服务器” > ***服务器名称*** > “刷新服务器”。

## <a name="set-up-the-target-environment"></a>设置目标环境

选择并验证目标资源。

1. 选择“准备基础结构” > “目标”。 选择要使用的 Azure 订阅。
2. 指定目标部署模型是基于 Azure 资源管理器还是经典模型。
3. Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。

   ![“目标”选项卡](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>创建复制策略

1. 打开 [Azure 门户](https://portal.azure.com)，选择“所有资源”。
2. 选择名为 ContosoVMVault 的恢复服务保管库。
3. 若要创建复制策略，请选择“Site Recovery 基础结构” > “复制策略” > “+复制策略”。
4. 在“创建复制策略”中，输入策略名称 VMwareRepPolicy。
5. 在“RPO 阈值”中，使用默认值 60 分钟。 此值确定创建恢复点的频率。 如果连续复制超出此限制，将生成警报。
6. 在“恢复点保留期”中，针对每个恢复点的保留时间长度使用默认值 24 小时。 对于本教程，请使用 72 小时。 可以将复制的虚拟机恢复到窗口中的任何点。
7. 在“应用一致快照频率”中，对于创建应用一致快照的频率使用默认值 60 分钟。 选择“确定”以创建策略。

   ![创建复制策略](./media/vmware-azure-tutorial/replication-policy.png)

此策略自动与配置服务器关联。 默认情况下会自动创建一个匹配策略，用于故障回复。 例如，如果复制策略是 **rep-policy**，则故障回复策略将是 **rep-policy-failback**。 从 Azure 启动故障回复之前，不会使用此策略。

## <a name="enable-replication"></a>启用复制

为 VM 启用复制后，Site Recovery 会安装移动服务。 可能要等 15 分钟或更长时间，更改才会生效并显示在门户中。

请按如下所述启用复制：

1. 选择“复制应用程序” > “源”。
2. 在“源”中选择配置服务器。
3. 在“计算机类型”中，选择“虚拟机”。
4. 在“vCenter/vSphere 虚拟机监控程序”中，选择管理 vSphere 主机的 vCenter 服务器，或选择该主机。
5. 选择进程服务器（配置服务器）。 然后选择“确定”。
6. 在“目标”中，选择要创建故障转移 VM 的订阅和资源组。 选择要在 Azure 中用于故障转移 VM 的部署模型（经典或 Resource Manager）。
7. 选择要用于复制数据的 Azure 存储帐户。
8. 选择 Azure VM 在故障转移后创建时所要连接的 Azure 网络和子网。
9. 选择“立即为选定的计算机配置”，将网络设置应用到选择保护的所有计算机。 选择“稍后配置”以选择每个计算机的 Azure 网络。
10. 在“虚拟机” > “选择虚拟机”中，选择要复制的每个虚拟机。 只能选择可以启用复制的计算机。 然后选择“确定”。
11. 在“属性” > “配置属性”中，选择进程服务器在计算机上自动安装移动服务时使用的帐户。
12. 在“复制设置” > “配置复制设置”中，检查是否选择了正确的复制策略。
13. 选择“启用复制”。

可以在“设置” > “作业” > “Site Recovery 作业”中，跟踪“启用保护”作业的进度。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。

若要监视添加的 VM，请在“配置服务器” > “上次联系时间”中查看上次发现 VM 的时间。 若要添加 VM 而不想要等待计划的发现，请突出显示配置服务器（不要选择它），然后选择“刷新”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [运行灾难恢复演练](site-recovery-test-failover-to-azure.md)

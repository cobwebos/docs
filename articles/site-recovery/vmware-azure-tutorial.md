---
title: 使用 Azure Site Recovery 设置到 Azure 的 VMware VM 灾难恢复
description: 了解如何使用 Azure Site Recovery 针对本地 VMware VM 设置到 Azure 的灾难恢复。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 878c3aa766559e455ee4456d84b86dc486e43fa5
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610677"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>针对本地 VMware VM 设置到 Azure 的灾难恢复

本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 服务启用本地 VMware VM 的复制，以便能够灾难恢复到 Azure。

本文是系列教程的第三篇文章，介绍如何为本地 VMware VM 设置到 Azure 的灾难恢复。 在上一篇教程中，我们已[准备本地 VMware 环境](vmware-azure-tutorial-prepare-on-premises.md)，以便能够灾难恢复到 Azure。


在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 设置源复制设置，以及一个本地 Site Recovery 配置服务器。
> * 设置复制目标。
> * 创建复制策略。
> * 为 VMware VM 启用复制。

> [!NOTE]
> 教程介绍了某个方案的最简单部署路径。 它们尽可能使用默认选项，并且不显示所有可能的设置和路径。 有关详细说明，请查看 Site Recovery 目录的“操作指南”部分所列的文章。

## <a name="before-you-start"></a>开始之前

完成前一篇教程：
1. 确保已[设置 Azure](tutorial-prepare-azure.md)，以便能够将本地 VMware 灾难恢复到 Azure。
2. 遵循[这些步骤](vmware-azure-tutorial-prepare-on-premises.md)准备本地 VMware 部署，以便能够灾难恢复到 Azure。
3. 在本教程中，我们介绍如何复制单个 VM。 若要部署多个 VMware VM，则应使用[部署规划器工具](https://aka.ms/asr-deployment-planner)。 [详细了解](site-recovery-deployment-planner.md)此工具。
4. 本教程使用了多种选项，你也可以采用不同的方式：
    - 本教程使用 OVA 模板创建配置服务器 VMware VM。 如果出于某种原因无法执行此操作，请遵照[这些说明](physical-manage-configuration-server.md)手动设置配置服务器。
    - 在本教程中，Site Recovery 会自动将 MySQL 下载并安装到配置服务器。 如果你愿意，可以改为手动设置它。 [了解详细信息](vmware-azure-deploy-configuration-server.md#configure-settings)。




## <a name="select-a-protection-goal"></a>选择保护目标

1. 在“恢复服务保管库”  中，选择保管库名称。 我们在此方案中使用 **ContosoVMVault**。
2. 在“入门”中，选择“Site Recovery”  ， 然后选择“准备基础结构”  。
3. 在“保护目标” > “计算机所在位置”中，选择“本地”    。
4. 在“要将计算机复制到何处?”中，选择“复制到 Azure”   。
5. 在“你的计算机是否已虚拟化”中，选择“是，带有 VMware vSphere 虚拟机监控程序”   。 然后选择“确定”。 



## <a name="set-up-the-source-environment"></a>设置源环境

在源环境中，需要部署一台高度可用的本地计算机来托管这些本地 Site Recovery 组件：

- **配置服务器**：配置服务器在本地和 Azure 之间协调通信并管理数据复制。
- **进程服务器**：进程服务器充当复制网关。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将其发送到 Azure 中的缓存存储帐户。 进程服务器还会将移动服务代理安装在要复制的 VM 上，并在本地 VMware VM 上执行自动发现。
- **主目标服务器**：主目标服务器处理从 Azure 进行故障回复期间产生的复制数据。


所有这些组件一起安装在称作“配置服务器”的一台本地计算机上。  默认情况下，为了实现 VMware 灾难恢复，我们会将一个高度可用的 VMware VM 设置为配置服务器。 为此，请下载一个已准备好的开放虚拟化应用程序 (OVA) 模板，并将该模板导入 VMware 以创建该 VM。

- 门户中提供了最新版本的配置服务器。 也可以直接从 [Microsoft 下载中心](https://aka.ms/asrconfigurationserver)下载该服务器。
- 如果出于某种原因无法使用 OVA 模板来设置 VM，请遵照[这些说明](physical-manage-configuration-server.md)手动设置配置服务器。
- 通过 OVF 模板提供的许可证是有效期为 180 天的评估许可证。 在 VM 上运行的 Windows 必须使用所需许可证进行激活。


### <a name="download-the-vm-template"></a>下载 VM 模板

1. 在保管库中，转到“准备基础结构” > “源”   。
2. 在“准备源”中，选择“+配置服务器”。  
3. 在“添加服务器”中，检查“VMware 的配置服务器”是否已显示在“服务器类型”中    。
4. 下载配置服务器的 OVA 模板。



## <a name="import-the-template-in-vmware"></a>在 VMware 中导入模板


1. 使用 VMWare vSphere 客户端登录到 VMware vCenter 服务器或 vSphere ESXi 主机。
2. 在“文件”菜单中，选择“部署 OVF 模板”以启动“部署 OVF 模板”向导    。

     ![OVF 模板](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. 在“选择源”中，输入下载的 OVF 所在的位置  。
4. 在“查看详细信息”中，选择“下一步”。  
5. 在“选择名称和文件夹”和“选择配置”中，接受默认设置   。
6. 在“选择存储”中，为获得最佳性能，请在“选择虚拟磁盘格式”中选择“Thick Provision Eager Zeroed”    。
7. 在余下的向导页中，接受默认设置。
8. 在“准备完成”上，若要使用默认设置来设置 VM，请选择“部署后打开” > “完成”    。

   > [!TIP]
   > 若要添加其他 NIC，请清除“部署后打开”   >   “完成”。 默认情况下，模板包含单个 NIC。 可以在部署后添加其他 NIC。

## <a name="add-an-additional-adapter"></a>添加其他适配器

若要将其他 NIC 添加到配置服务器，请在将服务器注册到保管库中之前添加它。 注册后不支持添加其他适配器。

1. 在 vSphere 客户端库存中，右键单击 VM 并选择“编辑设置”  。
2. 在“硬件”中，选择“添加” > “以太网适配器”    。 然后，选择“下一步”  。
3. 选择适配器类型和网络。
4. 若要在打开 VM 时连接虚拟 NIC，请选择“打开时连接”  。 选择“下一步”   >   “完成”。 然后选择“确定”。 


## <a name="register-the-configuration-server"></a>注册配置服务器

设置配置服务器后，将它注册到保管库中。

1. 通过 VMWare vSphere 客户端控制台打开 VM。
2. VM 将启动并进入 Windows Server 2016 安装体验。 接受许可协议，然后输入管理员密码。
3. 安装完成后，以管理员身份登录到 VM。
4. 首次登录时，会在数秒内启动 Azure Site Recovery 配置工具。
5. 输入用于向 Site Recovery 注册配置服务器的名称。 然后，选择“下一步”  。
6. 该工具会检查 VM 是否能够连接到 Azure。 建立连接后，选择“登录”以登录到 Azure 订阅  。 使用的凭据必须有权访问配置服务器所要注册到的保管库。 确保向此用户分配了必需的[角色](vmware-azure-deploy-configuration-server.md#azure-active-directory-permission-requirements)。
7. 该工具将执行一些配置任务，然后重新启动。
8. 再次登录到计算机。 在数秒内，配置服务器管理向导会自动启动。


### <a name="configure-settings-and-add-the-vmware-server"></a>配置设置并添加 VMware 服务器

完成配置服务器的设置和注册。 在继续之前，请确保已满足全部[先决条件](vmware-azure-deploy-configuration-server.md#prerequisites)，以便成功设置配置服务器。


1. 在配置服务器管理向导中，选择“设置连接”。  在下拉列表中，首先选择内置进程服务器用于发现的 NIC，将移动服务的安装推送到源计算机上，然后选择配置服务器用来与 Azure 连接的 NIC。 再选择“保存”  。 配置后无法更改此设置。
2. 在“选择恢复服务保管库”中，选择自己的 Azure 订阅以及相关的资源组和保管库  。
3. 在“安装第三方软件”中  ，接受许可协议。 选择“下载并安装”，安装 MySQL 服务器。  如果已将 MySQL 置于路径中，则可跳过此步骤。 了解[更多](vmware-azure-deploy-configuration-server.md#configure-settings)
4. 在“验证设备配置”中验证先决条件，然后继续  。
5. 在“配置 vCenter Server/vSphere ESXi 服务器”中，输入要复制的 VM 所在的 vCenter Server 或 vSphere 主机的 FQDN 或 IP 地址  。 输入服务器侦听的端口。 为保管库中的 VMware 服务器输入一个可用的友好名称。
6. 输入配置服务器用来连接到 VMware 服务器的用户凭据。 确保用户名和密码正确无误，并且属于要保护的虚拟机的 Administrators 组。 Site Recovery 将使用这些凭据自动发现可复制的 VMware VM。 选择“添加”，然后选择“继续”。  
7. 在“配置虚拟机凭据”中，输入将用于在 VM 上自动安装移动服务的用户名和密码（如果已启用复制）  。
    - 对于 Windows 计算机，该帐户在要复制的计算机上需有本地管理员特权。
    - 对于 Linux，请提供根帐户的详细信息。
8. 选择“完成配置”以完成注册  。
9. 完成注册后，打开 Azure 门户，验证配置服务器和 VMware 服务器是否在“恢复服务保管库”   > “管理”   > “Site Recovery 基础结构”   > “配置服务器”  中列出。


注册配置服务器后，Site Recovery 将使用指定的设置连接到 VMware 服务器，并发现 VM。

> [!NOTE]
> 帐户名出现在门户中可能需要 15 分钟或更长时间。 若要立即更新，请选择“配置服务器” > ***服务器名称*** > “刷新服务器”。  

## <a name="set-up-the-target-environment"></a>设置目标环境

选择并验证目标资源。

1. 选择“准备基础结构” > “目标”。   选择要使用的 Azure 订阅。 我们将使用资源管理器模型。
2. Site Recovery 会检查是否有一个或多个虚拟网络。 在此教程系列的[第一个教程](tutorial-prepare-azure.md)中设置 Azure 组件时，你应该已经有了这些。

   ![“目标”选项卡](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>创建复制策略

1. 打开 [Azure 门户](https://portal.azure.com)。 搜索并选择“恢复服务保管库”  。
2. 选择恢复服务保管库（在本教程中为 **ContosoVMVault**）。
3. 若要创建复制策略，请选择“Site Recovery 基础结构”   > “复制策略”   > “+复制策略”  。
4. 在“创建复制策略”  中，输入策略名称。 我们使用 **VMwareRepPolicy**。
5. 在“RPO 阈值”  中，使用默认值 60 分钟。 此值确定创建恢复点的频率。 如果连续复制超出此限制，将生成警报。
6. 在“恢复点保留期”中，指定每个恢复点的保留时间。  在本教程中，我们将使用 72 小时。 可以将复制的 VM 恢复到保留窗口中的任何点。
7. 在“应用一致性快照频率”  中，指定创建应用一致性快照的频率。 我们将使用默认值，即 60 分钟。 选择“确定”  以创建策略。

   ![创建复制策略](./media/vmware-azure-tutorial/replication-policy.png)

- 此策略自动与配置服务器关联。
- 默认情况下会自动创建一个匹配策略，用于故障回复。 例如，如果复制策略是 **rep-policy**，则故障回复策略将是 **rep-policy-failback**。 从 Azure 启动故障回复之前，不会使用此策略。

## <a name="enable-replication"></a>启用复制

为 VM 启用复制，如下所示：

1. 选择“复制应用程序” > “源”   。
2. 在“源”  中选择“本地”，然后在“源位置”中选择配置服务器。  
3. 在“计算机类型”中，选择“虚拟机”   。
4. 在“vCenter/vSphere 虚拟机监控程序”中选择 vSphere 主机或管理该主机的 vCenter 服务器。 
5. 选择进程服务器（默认安装在配置服务器 VM 上）。 然后选择“确定”。  每个进程服务器的运行状况状态是根据建议的限制和其他参数指示的。 选择一个正常运行的进程服务器。 不能选择[有严重错误的](vmware-physical-azure-monitor-process-server.md#process-server-alerts)进程服务器。 你可以[进行故障排除并解决](vmware-physical-azure-troubleshoot-process-server.md)错误**或者**设置一个[横向扩展进程服务器](vmware-azure-set-up-process-server-scale.md)。
6. 在“目标”中，选择要创建故障转移 VM 的订阅和资源组  。 我们将使用资源管理器部署模型。
7. 选择 Azure VM 在故障转移后创建时所要连接的 Azure 网络和子网。
8. 选择“立即为选定的计算机配置”，以便将网络设置应用到在其上启用了复制的所有 VM  。 选择“稍后配置”以选择每个计算机的 Azure 网络。 
9. 在“虚拟机” > “选择虚拟机”中，选择要复制的每个虚拟机   。 只能选择可以启用复制的计算机。 然后选择“确定”。  如果无法查看/选择特定的虚拟机，请[详细了解](https://aka.ms/doc-plugin-VM-not-showing)如何解决此问题。
10. 在“属性” > “配置属性”中，选择进程服务器在计算机上自动安装移动服务时使用的帐户。  
11. 在“复制设置” > “配置复制设置”中，检查是否选择了正确的复制策略。  
12. 选择“启用复制”。  为 VM 启用复制后，Site Recovery 会安装移动服务。
13. 可以在“设置” > “作业” > “Site Recovery 作业”中，跟踪“启用保护”作业的进度。     在“完成保护”  作业运行并且恢复点生成已完成后，计算机就可以进行故障转移了。
14. 可能要等 15 分钟或更长时间，更改才会生效并显示在门户中。
15. 若要监视添加的 VM，请在“配置服务器”   >   “上次联系时间”中查看上次发现 VM 的时间。 若要添加 VM 而不想要等待计划的发现，请突出显示配置服务器（不要选择它），然后选择“刷新”。 

## <a name="next-steps"></a>后续步骤
启用复制后，请运行演练以确保一切按预期进行。
> [!div class="nextstepaction"]
> [运行灾难恢复演练](site-recovery-test-failover-to-azure.md)

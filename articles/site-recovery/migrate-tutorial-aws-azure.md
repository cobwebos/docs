---
title: 使用 Azure Site Recovery 将 AWS 中的 VM 迁移到 Azure | Microsoft Docs
description: 本文介绍如何使用 Azure Site Recovery 将 Amazon Web Services (AWS) 中运行的 Windows VM 迁移到 Azure。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b0474ce532831e15738ec882dfdf451bc35d09cf
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737606"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>将 Amazon Web Services (AWS) VM 迁移到 Azure

本教程介绍如何使用 Azure Site Recovery 将 Amazon Web Services (AWS) 虚拟机 (VM) 迁移到 Azure VM。 将 AWS EC2 实例迁移到 Azure 时，VM 会被视作本地物理计算机。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 验证先决条件
> * 准备 Azure 资源
> * 为迁移准备 AWS EC2 实例
> * 部署配置服务器
> * 为 VM 启用复制
> * 测试故障转移，确保一切正常运行
> * 运行到 Azure 的一次性故障转移

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="prerequisites"></a>先决条件
- 确保要迁移的 VM 运行的是支持的 OS 版本。 支持的版本包括： 
    - Windows Server 2016
    - Windows Server 2012 R2
    - Windows Server 2012
    - 64 位版本的 Windows Server 2008 R2 SP1 或更高版本
    - Red Hat Enterprise Linux 6.7（仅限 HVM 虚拟化实例），使用 Citrix 半虚拟化驱动程序或 AWS 半虚拟化驱动程序。 不支持运行 Red Hat 半虚拟化驱动程序的实例。

- 必须在要复制的每个 VM 上安装移动服务。 

    > [!IMPORTANT]
    > 为 VM 启用复制后，Site Recovery 会自动安装此服务。 若要进行自动安装，必须在 EC2 实例上准备一个帐户，Site Recovery 将使用该帐户访问 VM。 可以使用域帐户或本地帐户。 
    > - 对于 Linux VM，该帐户应是源 Linux 服务器上的根帐户。 
    > - 对于 Windows VM，如果使用的不是域帐户，则在本地计算机上禁用远程用户访问控制：
    >
    >      在注册表的 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 下，添加 DWORD 项 **LocalAccountTokenFilterPolicy** 并将值设为 **1**。

- 一个单独的 EC2 实例，可以用作 Site Recovery 配置服务器。 此实例必须运行 Windows Server 2012 R2。

## <a name="prepare-azure-resources"></a>准备 Azure 资源

必须在 Azure 中准备一些资源，供迁移后的 EC2 实例使用。 其中包括存储帐户、保管库和虚拟网络。

### <a name="create-a-storage-account"></a>创建存储帐户

已复制计算机的映像保存在 Azure 存储中。 在从本地故障转移到 Azure 时，会从存储中创建 Azure VM。

1. 在 [Azure 门户](https://portal.azure.com)的左菜单中，选择“创建资源” > “存储” > “存储帐户”。
2. 输入存储帐户的名称。 在这些教程中，我们使用名称 **awsmigrated2017**。 名称必须符合以下条件：
    - 在 Azure 中唯一
    - 长度为 3 到 24 个字符
    - 仅包含数字和小写字母
3. 保留“部署模型”、“帐户类型”、“性能”和“需要安全转移”的默认值。
5. 为“复制”选择默认的“RA-GRS”。
6. 选择要用于本教程的订阅。
7. 对于“资源组”，选择“新建”。 在本示例中，使用 **migrationRG** 作为资源组名称。
8. 对于“位置”，选择“西欧”。
9. 选择“创建”以创建存储帐户。

### <a name="create-a-vault"></a>创建保管库

1. 在 [Azure 门户](https://portal.azure.com)中，选择“所有服务”。 搜索“恢复服务保管库”，然后将其选中。
2. 在“Azure 恢复服务保管库”页中，选择“添加”。
3. 对于“名称”，请输入 **myVault**。
4. 对于“订阅”，请选择要使用的订阅。
4. 对于“资源组”，请选择“使用现有”，然后选择“migrationRG”。
5. 对于“位置”，选择“西欧”。
5. 选择“固定到仪表板”即可从仪表板快速访问新的保管库。
7. 完成操作后，选择“创建”。

若要查看新保管库，请转到“仪表板” > “所有资源”。 新保管库也显示在“恢复服务保管库”主页上。

### <a name="set-up-an-azure-network"></a>设置 Azure 网络

在迁移（故障转移）后创建的 Azure VM 会加入此 Azure 网络。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源”****>“网络”****>
   ****“虚拟网络”。
3. 对于“名称”，请输入 **myMigrationNetwork**。
4. 保留“地址空间”的默认值。
5. 对于“订阅”，请选择要使用的订阅。
6. 对于“资源组”，请选择“使用现有”，然后选择“migrationRG”。
7. 对于“位置”，选择“西欧”。
8. 在“子网”下，保留“名称”和“IP 范围”的默认值。
9. 让“服务终结点”选项保持禁用状态。
10. 完成操作后，选择“创建”。

## <a name="prepare-the-infrastructure"></a>准备基础结构

在 Azure 门户的保管库页的“入门”部分选择“Site Recovery”，然后选择“准备基础结构”。 完成以下步骤。

### <a name="1-protection-goal"></a>1：保护目标

在“保护目标”页上选择以下值：

|    |  |
|---------|-----------|
| 计算机位于何处？ |选择“本地”。|
| 要将计算机复制到何处？ |选择“到 Azure”。|
| 计算机是否已虚拟化？ |选择“未虚拟化/其他”。|

完成后，选择“确定”即可转到下一部分。

### <a name="2-prepare-source"></a>2：准备源

在“准备源”页上，选择“+ 配置服务器”。

1. 使用运行 Windows Server 2012 R2 的 EC2 实例创建配置服务器，并将它注册到恢复保管库。
2. 在用作配置服务器的 EC2 实例 VM 上配置代理，以便它可以访问[服务 URL](site-recovery-support-matrix-to-azure.md)。
3. 下载 [Microsoft Azure Site Recovery 统一安装程序](http://aka.ms/unifiedinstaller_wus)。 可以将它下载到本地计算机，然后复制到用作配置服务器的 VM。
4. 选择“下载”按钮，下载保管库注册密钥。 将下载的文件复制到用作配置服务器的 VM。
5. 在该 VM 上，右键单击为 Microsoft Azure Site Recovery 统一安装程序下载的安装程序，然后选择“以管理员身份运行”。

    1. 在“准备工作”下选择“安装配置服务器和进程服务器”，然后选择“下一步”。
    2. 在“第三方软件许可证”中选择“我接受第三方许可协议”，然后选择“下一步”。
    3. 在“注册”中选择“浏览”，然后转到保管库注册密钥文件所在的位置。 选择“**下一步**”。
    4. 在“Internet 设置”中选择“在不使用代理服务器的情况下连接到 Azure Site Recovery”，然后选择“下一步”。
    5. “先决条件检查”页运行多个项的检查。 完成后，选择“下一步”。
    6. 在“MySQL 配置”中提供所需的密码，然后选择“下一步”。
    7. 在“环境详细信息”中选择“否”。 无需保护 VMware 计算机。 然后，选择“下一步”。
    8. 在“安装位置”中选择“下一步”，接受默认值。
    9. 在“网络选择”中选择“下一步”，接受默认值。
    10. 在“摘要”中选择“安装”。
    11. “安装进度”显示有关安装过程的信息。 完成后，选择“完成”。 此时会出现一个显示重启消息的窗口。 选择“确定”。 接下来会出现一个窗口，显示有关配置服务器连接通行短语的消息。 将通行短语复制到剪贴板，然后保存到其他安全位置。
6. 在 VM 上运行 cspsconfigtool.exe，以在配置服务器上创建一个或多个管理帐户。 请确保管理帐户在要迁移的 EC2 实例上具有管理员权限。

完成配置服务器设置后，请返回门户，选择为**配置服务器**创建的服务器。 选择“确定”，转到“3：准备目标”。

### <a name="3-prepare-target"></a>3：准备目标

在本部分，请输入在本教程前面的[准备 Azure 资源](#prepare-azure-resources)中创建的资源的相关信息。

1. 在“订阅”中，选择用于[准备 Azure](tutorial-prepare-azure.md)教程的 Azure 订阅。
2. 选择 **Resource Manager** 作为部署模型。
3. Site Recovery 会验证你是否有一个或多个兼容的 Azure 存储帐户和网络。 这些应该是在本教程前面的[准备 Azure 资源](#prepare-azure-resources)中创建的资源。
4. 完成后，请选择“确定”。


### <a name="4-prepare-replication-settings"></a>4：准备复制设置

必须先创建复制策略，然后才能启用复制。

1. 选择“复制并关联”。
2. 在“名称”中输入 **myReplicationPolicy**。
3. 其余选项均保留默认设置，然后选择“确定”以创建策略。 新策略会自动与配置服务器关联。

### <a name="5-select-deployment-planning"></a>5：选择部署规划

在“是否已完成部署规划”中选择“我将稍后进行”，然后选择“确定”。

完成“准备基础结构”下所有五个部分的操作后，请选择“确定”。


## <a name="enable-replication"></a>启用复制

为要迁移的各 VM 启用复制。 启用复制后，Site Recovery 会自动安装移动服务。

1. 转到 [Azure 门户](htts://portal.azure.com)。
1. 在保管库相应页面的“入门”下，选择“Site Recovery”。
2. 在“适用于本地计算机和 Azure VM”下，选择“步骤 1: 复制应用程序”。 使用以下信息完成向导页面。 完成后，在每个页面上选择“确定”：
    - 1：配置源

    |  |  |
    |-----|-----|
    | 源： | 选择“本地”。|
    | 源位置：| 输入配置服务器 EC2 实例的名称。|
    |计算机类型： | 选择“物理计算机”。|
    | 进程服务器： | 从下拉列表中选择配置服务器。|

    - 2：配置目标

    |  |  |
    |-----|-----|
    | 目标： | 保留默认值。|
    | 订阅： | 选择已在使用的订阅。|
    | 故障转移后的资源组：| 使用在[准备 Azure 资源](#prepare-azure-resources)中创建的资源组。|
    | 故障转移后的部署模型： | 选择“Resource Manager”。|
    | 存储帐户： | 选择在[准备 Azure 资源](#prepare-azure-resources)中创建的存储帐户。|
    | Azure 网络： | 选择“立即对选定的计算机进行配置”。|
    | 故障转移后的 Azure 网络： | 选择在[准备 Azure 资源](#prepare-azure-resources)中创建的网络。|
    | 子网： | 在下拉列表中选择“默认”。|

    - 3：选择物理计算机

      选择“物理计算机”，然后输入要迁移的 EC2 实例的“名称”、“IP 地址”和“OS 类型”的值。 选择“确定”。

    - 4：配置属性

      选择在配置服务器上创建的帐户，然后选择“确定”。

    - 5：配置复制设置

      确保在下拉列表中选定的复制策略是“myReplicationPolicy”，然后选择“确定”。

3. 向导完成后，选择“启用复制”。

若要跟踪“启用保护”作业的进度，请转到“监视和报告” > “作业” > “Site Recovery 作业”。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。        

为 VM 启用复制后，可能要等 15 分钟或更长时间，更改才会生效并显示在门户中。

## <a name="run-a-test-failover"></a>运行测试故障转移

运行测试故障转移时，会发生以下事件：

- 运行先决条件检查，确保故障转移所需的所有条件都已就绪。
- 故障转移处理数据，以便创建 Azure VM。 如果选择最新恢复点，则根据该数据创建恢复点。
- 使用上一步中处理的数据创建 Azure VM。

在门户中运行测试故障转移：

1. 在保管库的相应页上，转到“受保护的项” > “复制的项”。 选择 VM，然后选择“测试故障转移”。
2. 选择要用于故障转移的恢复点：
    - **最新处理**：将 VM 故障转移到由 Site Recovery 处理的最新恢复点。 将显示时间戳。 使用此选项时，无需费时处理数据，因此恢复时间目标 (RTO) 会较低。
    - **最新的应用一致**：此选项将所有 VM 故障转移到最新的应用一致恢复点。 将显示时间戳。
    - **自定义**：选择任何恢复点。

3. 在“测试故障转移”中，选择 Azure VM 在故障转移之后要连接到的目标 Azure 网络。 它应该是在[准备 Azure 资源](#prepare-azure-resources)中创建的网络。
4. 选择“确定”，开始故障转移。 若要跟踪进度，请选择要查看其属性的 VM。 也可以在保管库的相应页面上选择“测试故障转移”作业。 为此，请选择“监视和报告” > “作业” >  “Site Recovery 作业”。
5. 故障转移完成后，副本 Azure VM 会显示在 Azure 门户中。 若要查看该 VM，请选择“虚拟机”。 请确保 VM 的大小适当、已连接到正确的网络，并且正在运行。
6. 现在应该能够连接到 Azure 中复制的虚拟机。
7. 若要删除在测试故障转移期间创建的 Azure VM，请在恢复计划中选择“清理测试故障转移”。 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。

在某些情况下，故障转移需要进行额外的处理。 完成处理需要 8 到 10 分钟的时间。

## <a name="migrate-to-azure"></a>迁移到 Azure

对 EC2 实例运行真正的故障转移，将其迁移到 Azure VM：

1. 在“受保护的项” > “复制的项”中选择 AWS 实例，然后选择“故障转移”。
2. 在“故障转移”中，选择要故障转移到其中的“恢复点”。 选择最新恢复点。
3. 如果希望 Site Recovery 在触发故障转移之前尝试关闭源虚拟机，请选择“在开始故障转移之前关闭计算机”。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。
4. 确保 VM 出现在“复制的项”中。
5. 右键单击每个 VM，然后选择“完成迁移”。 此操作将完成迁移过程，停止 AWS VM 复制，并停止 VM 的 Site Recovery 计费。

    ![完成迁移](./media/migrate-tutorial-aws-azure/complete-migration.png)

> [!WARNING]
> 请勿取消正在进行的故障转移。 在故障转移开始前，VM 复制已停止。 如果取消正在进行的故障转移，故障转移会停止，但 VM 将不再进行复制。  


## <a name="next-steps"></a>后续步骤

本文介绍了如何将 AWS EC2 实例迁移到 Azure VM。 若要详细了解 Azure VM，请继续学习 Windows VM 的相关教程。

> [!div class="nextstepaction"]
> [Azure Windows 虚拟机教程](../virtual-machines/windows/tutorial-manage-vm.md)

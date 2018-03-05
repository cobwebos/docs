---
title: "使用 Azure Site Recovery 将 AWS 中的 VM 迁移到 Azure | Microsoft Docs"
description: "本文介绍如何使用 Azure Site Recovery 将 Amazon Web Services (AWS) 中运行的 Windows VM 迁移到 Azure。"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 59a09b5d67391f2b48d338d721369f14ed6b4ede
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>将 Amazon Web Services (AWS) VM 迁移到 Azure

本教程介绍如何使用 Site Recovery 将 Amazon Web Services (AWS) 虚拟机 (VM) 迁移到 Azure VM。 将 EC2 实例迁移到 Azure 时，VM 会被视作本地物理计算机。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 准备 Azure 资源
> * 为迁移准备 AWS EC2 实例
> * 部署配置服务器
> * 为 VM 启用复制
> * 测试故障转移，确保一切正常运行
> * 面向 Azure 运行一次性故障转移

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prepare-azure-resources"></a>准备 Azure 资源

需要在 Azure 中准备一些资源供迁移后的 EC2 实例使用。 其中包括存储帐户、保管库和虚拟网络。

### <a name="create-a-storage-account"></a>创建存储帐户

已复制计算机的映像保存在 Azure 存储中。 在从本地故障转移到 Azure 时，会从该存储中创建 Azure VM。

1. 在 [Azure 门户](https://portal.azure.com)菜单中，单击“创建资源” > “存储” > “存储帐户”。
2. 输入存储帐户的名称。 本教程使用名称 **awsmigrated2017**。 该名称在 Azure 中必须唯一，必须为 3 到 24 个字符，并且仅包含数字和小写字母。
3. 保留“部署模型”、“帐户类型”、“性能”和“需要安全转移”的默认值。
5. 为“复制”选择默认的“RA-GRS”。
6. 选择要用于本教程的订阅。
7. 对于“资源组”，选择“新建”。 在本示例中，使用 **migrationRG** 作为名称。
8. 选择“西欧”作为位置。
9. 单击“创建”以创建存储帐户。

### <a name="create-a-vault"></a>创建保管库

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“所有服务”，搜索并选择“恢复服务保管库”。
2. 在“恢复服务保管库”页上，单击页面左上角的“+ 添加”。
3. 对于“名称”，键入 *myVault*。
4. 对于“订阅”，选择相应订阅。
4. 对于“资源组”，选择“使用现有项”和“migrationRG”。
5. 在“位置”中，选择“西欧”。
5. 若要从仪表板快速访问新的保管库，请选择“固定到仪表板”。
7. 完成后，单击“创建”。

新保管库显示在“仪表板” > “所有资源”中，以及“恢复服务保管库”主页上。

### <a name="set-up-an-azure-network"></a>设置 Azure 网络

在迁移（故障转移）后创建的 Azure VM 会加入此网络。

1. 在 [Azure 门户](https://portal.azure.com)中，单击“创建资源”****>****“网络”>
   ****“虚拟网络”。
3. 对于“名称”，键入 *myMigrationNetwork*。
4. 保留“地址空间”的默认值。
5. 对于“订阅”，选择相应订阅。
6. 对于“资源组”，选择“使用现有项”并从下拉列表中选择“migrationRG”。
7. 对于“位置”，选择“西欧”。
8. 保留“子网”的“名称”和“IP 范围”默认值。
9. 保留“服务终结点”的禁用状态。
10. 完成后，单击“创建”。


## <a name="prepare-the-ec2-instances"></a>准备 EC2 实例

需要一个或多个要迁移的 VM。 这些 EC2 实例应运行 64 位版本的 Windows Server 2008 R2 SP1 或更高版本、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016 或 Red Hat Enterprise Linux 6.7（仅限 HVM 虚拟化实例）。 服务器上只能有 Citrix PV 或 AWS PV 驱动程序。 不支持运行 RedHat PV 驱动程序的实例。

必须在要复制的每个 VM 上安装 Mobility Service。 为 VM 启用复制后，Site Recovery 会自动安装此服务。 若要进行自动安装，需在 EC2 实例上准备一个帐户，Site Recovery 将使用该帐户访问 VM。

可以使用域帐户或本地帐户。 对于 Linux VM，该帐户应是源 Linux 服务器上的根帐户。 对于 Windows VM，如果使用的不是域帐户，则在本地计算机上禁用远程用户访问控制：

  - 在注册表的 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 下，添加 DWORD 项 **LocalAccountTokenFilterPolicy** 并将值设为 1。

还需要一个单独的 EC2 实例，以便用作 Site Recovery 配置服务器。 此实例必须运行 Windows Server 2012 R2。


## <a name="prepare-the-infrastructure"></a>准备基础结构

在保管库的门户页上，从“入门”部分中选择“Site Recovery”，然后单击“准备基础结构”。

### <a name="1-protection-goal"></a>1 保护目标

在“保护目标”页上选择以下值：

|    |  |
|---------|-----------|
| 计算机位于何处？ | **本地**|
| 要将计算机复制到何处？ |**到 Azure**|
| 计算机是否已虚拟化？ | **未虚拟化/其他**|

完成后，单击“确定” 前往下一部分。

### <a name="2-source-prepare"></a>2 源准备

在“准备源”页上，单击“+ 配置服务器”。

1. 使用运行 Windows Server 2012 R2 的 EC2 实例创建配置服务器，并将它注册到恢复保管库。

2. 在用作配置服务器的 EC2 实例 VM 上配置代理，以便它可以访问[服务 URL](site-recovery-support-matrix-to-azure.md)。

3. 下载 [Microsoft Azure Site Recovery 统一安装](http://aka.ms/unifiedinstaller_wus)程序。 可以将它下载到本地计算机，然后复制到用作配置服务器的 VM。

4. 单击“下载”按钮下载保管库注册密钥。 将下载的文件复制到用作配置服务器的 VM。

5. 在该 VM 上，右键单击为 **Microsoft Azure Site Recovery 统一安装**下载的安装程序，选择“以管理员身份运行”。

    1. 在“开始之前”中，选择“安装配置服务器和进程服务器”，然后单击“下一步”。
    2. 在“第三方软件许可证”中，选择“我接受第三方许可协议。”并 单击“下一步”。
    3. 在“注册”中，单击“浏览”，导航到保管库注册密钥文件所在的位置，然后单击“下一步”。
    4. 在“Internet 设置”中，选择“在不使用代理服务器的情况下连接到 Azure Site Recovery。”并 单击“下一步”。
    5. 在“必备项检查”页上，它会运行多次检查。 完成后，单击“下一步”。
    6. 在“MySQL 配置”中，提供所需的密码，然后单击“下一步”。
    7. 在“环境详细信息”中，选择“否”（无需保护 VMware 计算机），然后单击“下一步”。
    8. 在“安装位置”中，单击“下一步”接受默认值。
    9. 在“网络选择”中，单击“下一步”接受默认值。
    10. 在“摘要”中，单击“安装”。
    11. “安装进度”显示有关安装进度的信息。 完成后，单击“完成”。 系统会弹出一个窗口，指示可能需要重启，请单击“确定”。 此外还会弹出一个有关配置服务器连接密码的窗口，请将密码复制到剪贴板并保存在安全的地方。

6. 在 VM 上运行 **cspsconfigtool.exe**，以在配置服务器上创建一个或多个管理帐户。 请确保管理帐户在要迁移的 EC2 实例上具有管理员权限。

完成配置服务器设置后，请返回门户，选择刚刚为“配置服务器”创建的服务器，然后单击“确定”* 前往步骤 3“目标准备”。

### <a name="3-target-prepare"></a>3 目标准备

在本部分中，输入在完成本教程前面的[准备 Azure 资源](#prepare-azure-resources)部分时创建的资源的相关信息。

1. 在“订阅”中，选择用于[准备 Azure](tutorial-prepare-azure.md)教程的 Azure 订阅。
2. 选择 **Resource Manager** 作为部署模型。
3. Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。 这些资源应该是在完成本教程前面的[准备 Azure 资源](#prepare-azure-resources)部分时创建的
4. 完成后，单击“确定”。


### <a name="4-replication-settings-prepare"></a>4 复制设置准备

需要创建复制策略才能启用复制

1. 单击“+ 复制并关联”。
2. 在“名称”中，键入 **myReplicationPolicy**。
3. 其余选项均保留默认设置，然后单击“确定”创建策略。 新策略会自动与配置服务器关联。

### <a name="5-deployment-planning-select"></a>5 部署规划选择

在“是否已完成部署规划?”中，从下拉列表中选择“我将稍后进行”，然后单击“确定”。

在“准备基础结构”的 5 个部分全部完成后，单击“确定”。


## <a name="enable-replication"></a>启用复制

为要迁移的各 VM 启用复制。 启用复制后，Site Recovery 会自动安装 Mobility Service。

1. 打开 [Azure 门户](htts://portal.azure.com)。
1. 在保管库相应页面的“入门”下，单击“Site Recovery”。
2. 在“适用于本地计算机和 Azure VM”下，单击“步骤 1: 复制应用程序”。 在向导页上填写以下信息，并在各页面完成后单击“确定”：
    - 1 源配置

    |  |  |
    |-----|-----|
    | 源： | **本地**|
    | 源位置：| 配置服务器 EC2 实例的名称。|
    |计算机类型： | **物理计算机**|
    | 进程服务器： | 从下拉列表中选择配置服务器。|

    - 2 目标配置

    |  |  |
    |-----|-----|
    | 目标： | 保留默认值。|
    | 订阅： | 选择已在使用的订阅。|
    | 故障转移后的资源组：| 使用在[准备 Azure 资源](#prepare-azure-resources)部分中创建的资源组。|
    | 故障转移后的部署模型： | 选择“资源管理器”|
    | 存储帐户： | 选择在[准备 Azure 资源](#prepare-azure-resources)部分中创建的存储帐户。|
    | Azure 网络： | 选择“立即对选定的计算机进行配置”|
    | 故障转移后的 Azure 网络： | 选择在[准备 Azure 资源](#prepare-azure-resources)部分中创建的网络。|
    | 子网： | 从下拉列表中选择“默认”。|

    - 3 物理计算机选择

        单击“+ 物理计算机”，输入要迁移的 EC2 实例的“名称”、“IP 地址”和“OS 类型”，然后单击“确定”。

    - 4 属性 配置属性

        从下拉列表中选择在配置服务器上创建的帐户，单击“确定”。

    - 5 复制设置 配置复制设置

        确保在下拉列表中选定的复制策略是“myReplicationPolicy”，然后单击“确定”。

3. 向导完成后，单击“启用复制”。


可以在“监视和报告” > “作业” > “Site Recovery 作业”中，跟踪“启用保护”作业的进度。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。        

为 VM 启用复制后，可能要等 15 分钟或更长时间，更改才会生效并显示在门户中。

## <a name="run-a-test-failover"></a>运行测试故障转移

运行测试故障转移时需执行下列操作：

1. 运行必备项检查，确保故障转移所需的所有条件都已就绪。
2. 故障转移处理数据，以便创建 Azure VM。 如果选择最新恢复点，则根据该数据创建恢复点。
3. 使用上一步中处理的数据创建 Azure VM。

在门户中运行测试故障转移，如下所示：

1. 在保管库的相应页上，转到“受保护的项” > “复制的项”> 单击该 VM >“+ 测试故障转移”。

2. 选择要用于故障转移的恢复点：
    - **最新处理**：将 VM 故障转移到由 Site Recovery 处理的最新恢复点。 将显示时间戳。 使用此选项时，无需费时处理数据，因此 RTO（恢复时间目标）会较低。
    - **最新的应用一致**：此选项将所有 VM 故障转移到最新的应用一致恢复点。 将显示时间戳。
    - **自定义**：选择任何恢复点。
3. 在“测试故障转移”中，选择 Azure VM 在故障转移之后要连接到的目标 Azure 网络。 它应该是在[准备 Azure 资源](#prepare-azure-resources)部分中创建的网络。
4. 单击“确定”开始故障转移。 可以通过单击虚拟机打开其属性来跟踪进度。 也可以在保管库相应页面上的 **监视和报告**  >  **作业**  >
    **Site Recovery 作业** 中单击 **测试故障转移** 作业。
5. 故障转移完成后，副本 Azure VM 会显示在 Azure 门户 >“虚拟机”中。 请确保虚拟机的大小适当、已连接到正确的网络，并且正在运行。
6. 现在应该能够连接到 Azure 中复制的虚拟机。
7. 若要删除在测试故障转移期间创建的 Azure VM，请在恢复计划上单击“清理测试故障转移”。 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。

在某些情况下，故障转移需要大约八到十分钟的时间完成其他进程。


## <a name="migrate-to-azure"></a>迁移到 Azure

对 EC2 实例运行真正的故障转移，将其迁移到 Azure VM。

1. 在“受保护的项” > “复制的项”中，单击 AWS 实例 >“故障转移”。
2. 在“故障转移”中，选择要故障转移到的“恢复点”。 选择最新恢复点。
3. 如果希望 Site Recovery 在触发故障转移之前尝试关闭源虚拟机，请选择“在开始故障转移之前关闭计算机”。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。
4. 确保 VM 出现在“复制的项”中。
5. 右键单击每个 VM >“完成迁移”。 此操作将完成迁移过程，停止 AWS VM 复制，并停止 VM 的 Site Recovery 计费。

    ![完成迁移](./media/migrate-tutorial-aws-azure/complete-migration.png)

> [!WARNING]
> **请勿取消正在进行的故障转移**：在故障转移开始前，停止 VM 复制。 如果取消正在进行的故障转移，故障转移会停止，但 VM 将不再进行复制。  




## <a name="next-steps"></a>后续步骤

在本主题中，已了解如何将 AWS EC2 实例迁移到 Azure VM。 若要详细了解 Azure VM，请继续学习 Windows VM 的相关教程。

> [!div class="nextstepaction"]
> [Azure Windows 虚拟机教程](../virtual-machines/windows/tutorial-manage-vm.md)

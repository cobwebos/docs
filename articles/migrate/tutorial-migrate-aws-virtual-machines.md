---
title: 发现、评估 Amazon Web Services (AWS) EC2 VM 并将其迁移到 Azure
description: 本文介绍如何使用 Azure Migrate 将 AWS VM 迁移到 Azure。
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: MVC
ms.openlocfilehash: 9e26268010e4287d1f98e99389ffeddf3e4747ce
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88611426"
---
# <a name="discover-assess-and-migrate-amazon-web-services-aws-vms-to-azure"></a>发现、评估 Amazon Web Services (AWS) VM 并将其迁移到 Azure

本教程介绍如何使用 Azure Migrate 发现、评估 Amazon Web Services (AWS) 虚拟机 (VM) 并将其迁移到 Azure VM：服务器评估和 Azure Migrate:服务器迁移工具。

> [!NOTE]
> 将 AWS VM 迁移到 Azure 的方式与迁移物理服务器相同。

在本教程中，您将学习如何执行以下操作：
> [!div class="checklist"]
> * 验证迁移的先决条件。
> * 让 Azure 资源准备好使用 Azure Migrate：服务器迁移。 设置要与 Azure Migrate 配合使用的 Azure 帐户和资源的权限。
> * 让 AWS EC2 实例做好迁移准备。
> * 添加 Azure Migrate：Azure Migrate 中心内的服务器迁移工具。
> * 设置复制设备并部署配置服务器。
> * 在要迁移的 AWS VM 上安装移动服务。
> * 为 VM 启用复制。
> * 跟踪和监视复制状态。 
> * 运行测试迁移，确保一切按预期正常进行。
> * 运行到 Azure 的完整迁移。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="discover-and-assess"></a>发现和评估

建议你先执行 VM 发现和迁移评估，然后再迁移到 Azure。 该评估可帮助你适当调整要迁移到 Azure 的 AWS VM，并预估 Azure 可能的运行成本。

按如下述设置评估：

1. 按照[教程](./tutorial-prepare-physical.md)设置 Azure 并准备 AWS VM 进行评估。 请注意：

    - Azure Migrate 在发现 AWS 实例时使用密码验证。 AWS 实例默认不支持密码验证。 需要先启用密码验证，才能发现实例。
        - 对于 Windows 计算机，请允许 WinRM 端口 5986 (HTTPS) 和 5985 (HTTP)。 这将允许 WMI 调用。 如果设置 
        - 对于 Linux 计算机：
            1. 登录到每台 Linux 计算机。
            2. Open the sshd_config file : vi /etc/ssh/sshd_config
            3. 在文件中，定位到 PasswordAuthentication 行，然后将值改为“yes”。 
            4. 保存文件并将其关闭。 重启 ssh 服务。

2. 然后，按照此[教程](./tutorial-assess-physical.md)设置 Azure Migrate 项目和设备，以发现和评估 AWS VM。

尽管建议你尝试进行评估，但执行评估并不是迁移 VM 的必需步骤。



## <a name="prerequisites"></a>先决条件 

- 确保要迁移的 AWS VM 正在运行受支持的操作系统版本。 出于迁移目的，AWS VM 将被视为物理计算机。 请查看[受支持的操作系统](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)，了解物理服务器迁移工作流。 建议执行测试迁移（测试故障转移），验证 VM 是否按预期运行，然后再继续实际的迁移。
- 要迁移到 Azure，请确保 AWS VM 符合[支持的配置](./migrate-support-matrix-physical-migration.md#physical-server-requirements)。
- 验证复制到 Azure 的 AWS VM 是否符合 [Azure VM 要求](./migrate-support-matrix-physical-migration.md#azure-vm-requirements)。
- 在将 VM 迁移到 Azure 之前，需要在 VM 上进行一些更改。
    - 对于某些操作系统，Azure Migrate 可自动做出这些更改。
    - 在开始迁移之前，必须做出这些更改。 如果在做出更改之前迁移 VM，VM 可能无法在 Azure 中启动。
查看在 [Windows](prepare-for-migration.md#windows-machines) 和 [Linux](prepare-for-migration.md#linux-machines) 上需要进行的更改。

### <a name="prepare-azure-resources-for-migration"></a>让 Azure 资源准备好进行迁移

让 Azure 准备好使用 Azure Migrate 进行迁移：服务器迁移工具。

**任务** | **详细信息**
--- | ---
**创建 Azure Migrate 项目** | Azure 帐户需要“参与者”或“所有者”权限才能创建项目。
**验证 Azure 帐户的权限** | Azure 帐户需要有权创建 VM 并将数据写入 Azure 托管磁盘。

### <a name="assign-permissions-to-create-project"></a>分配创建项目的权限

1. 在 Azure 门户中打开订阅，然后选择“访问控制(IAM)”。
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。
3. 你应该拥有“参与者”或“所有者”权限。 
    - 如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。
    - 如果你不是订阅所有者，请让所有者分配该角色。

### <a name="assign-azure-account-permissions"></a>分配 Azure 帐户权限

将“虚拟机参与者”角色分配给 Azure 帐户。 这提供了执行以下操作的权限：

- 在所选资源组中创建 VM。
- 在所选虚拟网络中创建 VM。
- 写入 Azure 托管磁盘。 

### <a name="create-an-azure-network"></a>创建 Azure 网络

[设置](../virtual-network/manage-virtual-network.md#create-a-virtual-network) Azure 虚拟网络 (VNet)。 复制到 Azure 时，所创建的 Azure VM 会加入到在设置迁移时指定的 Azure VNet。

## <a name="prepare-aws-instances-for-migration"></a>让 AWS 实例准备好进行迁移

若要准备好进行 AWS 到 Azure 的迁移，需要准备和部署一个用于迁移的复制设备。

### <a name="prepare-a-machine-for-the-replication-appliance"></a>为复制设备准备计算机

“Azure Migrate:Azure Migrate 使用复制设备将计算机复制到 Azure。 复制设备运行以下组件。

- **配置服务器**：配置服务器会协调 AWS 环境与 Azure 之间的通信并管理数据复制。
- **进程服务器**：进程服务器充当复制网关。 它会接收复制数据，通过缓存、压缩和加密对其进行优化，然后将它发送到 Azure 中的缓存存储帐户。

为设备部署做好准备，如下所述：

- 设置单独的 EC2 VM 以托管复制设备。 此实例必须正在运行 Windows Server 2012 R2 或 Windows Server 2016。 [查看](./migrate-replication-appliance.md#appliance-requirements)设备的硬件、软件和网络要求。
- 该设备不应安装在要复制的源 VM 上，也不应安装在以前可能已安装的 Azure Migrate 发现和评估设备上。 它应部署在其他 VM 上。
- 要迁移的源 AWS VM 应具有到复制设备的网络线路。 配置所需的安全组规则来启用它。 建议将复制设备部署到要迁移的源 VM 所在的 VPC 中。 如果复制设备需要位于其他 VPC 中，则需要通过 VPC 对等互连来连接这些 VPC。
- 源 AWS VM 在端口 HTTPS 443（控制通道业务流程）和 TCP 9443（数据传输）上与复制设备进行入站通信，从而管理复制和传输复制数据。 然后，复制设备会通过端口 HTTPS 443 出站来协调复制数据并将该数据发送到 Azure。 若要配置这些规则，请编辑安全组入站/出站规则，在其中加入相应的端口和源 IP 信息。

   ![AWS 安全组 ](./media/tutorial-migrate-aws-virtual-machines/aws-security-groups.png)
     
 
   ![编辑安全设置 ](./media/tutorial-migrate-aws-virtual-machines/edit-security-settings.png)

- 复制设备使用 MySQL。 查看在设备上安装 MySQL 的[选项](migrate-replication-appliance.md#mysql-installation)。
- 查看复制设备访问[公有云](migrate-replication-appliance.md#url-access)和[政府云](migrate-replication-appliance.md#azure-government-url-access)时所需的 Azure URL。

## <a name="add-the-server-migration-tool"></a>添加服务器迁移工具

设置一个 Azure Migrate 项目，并将服务器迁移工具添加到其中。

1. 在 Azure 门户中选择“所有服务”，然后搜索 **Azure Migrate**。
2. 在“服务”下选择“Azure Migrate”。 
3. 在“概述”中，单击“评估和迁移服务器”。 
4. 在“发现、评估和迁移服务器”下，单击“评估和迁移服务器”。 

    ![发现和评估服务器](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. 在“发现、评估和迁移服务器”中，单击“添加工具”。 
6. 在“迁移项目”中选择你的 Azure 订阅，并创建一个资源组（如果没有）。
7. 在“项目详细信息”中，指定项目名称以及要在其中创建项目的地理位置，然后单击“下一步”。  查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。
    - 项目地理位置仅用于存储从 AWS 计算机中收集的元数据。
    - 运行迁移时，可以选择任一目标区域。

    ![创建 Azure Migrate 项目](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

8. 在“选择评估工具”中，选择“暂时跳过添加评估工具” > “下一步”。  
9. 在“选择迁移工具”中，选择“Azure Migrate: 服务器迁移” > “下一步”。
10. 在“检查 + 添加工具”中检查设置，然后单击“添加工具”。 
11. 添加工具后，它会显示在 Azure Migrate 项目的“服务器” > “迁移工具”中。 

## <a name="set-up-the-replication-appliance"></a>设置复制设备

第一个迁移步骤是设置复制设备。 若要设置设备来迁移 AWS VM，则必须下载设备的安装程序文件，然后在[准备好的 VM](#prepare-a-machine-for-the-replication-appliance) 上运行。

### <a name="download-the-replication-appliance-installer"></a>下载复制设备安装程序

1. 在 Azure Migrate 项目中选择“服务器”，在“Azure Migrate: 服务器迁移”中，单击“发现”。

    ![发现 VM](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. 在“发现计算机” > “计算机是否已虚拟化?”中，单击“未虚拟化/其他”。  
3. 在“目标区域”中，选择要将计算机迁移到的 Azure 区域。
4. 选择“确认迁移的目标区域为 <区域名称>”。
5. 单击“创建资源”。 随即会在后台创建一个 Azure Site Recovery 保管库。
    - 如果已设置使用 Azure Migrate 服务器迁移进行迁移，则无法配置目标选项，因为之前已设置了资源。
    - 单击此按钮后，无法更改此项目的目标区域。
    - 若要将 VM 迁移到其他区域，需要创建一个新的/不同的 Azure Migrate 项目。

6. 在“是否安装新的复制设备?”中，选择“安装复制设备”。 
7. 在“下载并安装复制设备软件”中，下载设备安装程序和注册密钥。 需要使用该密钥来注册设备。 下载的密钥有效期为 5 天。

    ![下载提供程序](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. 将设备安装程序文件和密钥文件复制到为复制设备创建的 Windows Server 2016 或 Windows Server 2012 AWS VM。
9. 如以下过程中所述，运行复制设备安装程序文件。  
    9.1. 在“准备工作”下选择“安装配置服务器和进程服务器”，然后选择“下一步”。   
    9.2 在“第三方软件许可证”中选择“我接受第三方许可协议”，然后选择“下一步”  。   
    9.3 在“注册”中选择“浏览”，然后转到保管库注册密钥文件所在的位置 。 选择“**下一页**”。  
    9.4 在“Internet 设置”中选择“在不使用代理服务器的情况下连接到 Azure Site Recovery”，然后选择“下一步”  。  
    9.5 “先决条件检查”页面会运行多个项的检查。 完成后，选择“下一步”。  
    9.6 在“MySQL 配置”中提供 MySQL DB 的密码，然后选择“下一步” 。  
    9.7 在“环境详细信息”中选择“否” 。 无需保护 VM。 然后，选择“下一步”。  
    9.8 在“安装位置”中选择“下一步”，接受默认值 。  
    9.9 在“网络选择”中选择“下一步”，接受默认值 。  
    9.10 在“摘要”中选择“安装” 。   
    9.11 “安装进度”显示有关安装过程的信息。 完成后，选择“完成”。 此时会出现一个显示重启消息的窗口。 选择“确定”。   
    9.12 接下来会出现一个窗口，显示有关配置服务器连接通行短语的消息。 将通行短语复制到剪贴板，并将它保存在源 VM 上的临时文本文件中。 在稍后的移动服务安装过程中，需要用到此通行短语。
10. 安装完成后，设备配置向导将自动启动（也可使用在设备的桌面上创建的 cspsconfigtool 快捷方式手动启动该向导）。 使用向导的“管理帐户”选项卡可添加要用于移动服务的推送安装的帐户详细信息。 在本教程中，我们将在要复制的源 VM 上手动安装移动服务，因此请在此步骤中创建一个虚拟帐户，然后继续。 可以提供以下详细信息来创建虚拟帐户：“guest”作为友好名称，“username”作为用户名，“password”作为帐户密码。 你将在“启用复制”阶段使用此虚拟帐户。 
11. 设备已安装并重启后，在“发现计算机”中的“选择配置服务器”内选择新设备，然后单击“完成注册”。   “完成注册”步骤会执行最终的几个任务来准备复制设备。

    ![完成注册](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="install-the-mobility-service"></a>安装移动服务

必须在要迁移的源 AWS VM 上安装移动服务代理。 复制设备上已提供代理安装程序。 请找到适当的安装程序，并在要迁移的每台计算机上安装该代理。 请按如下所述执行此操作：

1. 登录到复制设备。
2. 导航到 **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**。
3. 找到适用于源 AWS VM 操作系统和版本的安装程序。 查看[支持的操作系统](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)。
4. 将安装程序文件复制到要迁移的源 AWS VM。
5. 请确保具有在安装复制设备时创建且已保存的通行短语文本文件。
    - 如果忘记保存通行短语，可通过此步骤复制设备上查看通行短语。 在命令行中，运行 **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** 查看当前的通行短语。
    - 现在，将该通行短语复制到剪贴板，并将其保存在源 VM 上的临时文本文件中。

### <a name="installation-guide-for-windows-aws-vms"></a>Windows AWS VM 安装指南

1. 将安装程序文件的内容解压缩到 AWS VM 上的某个本地文件夹（例如 C:\Temp），如下所示：

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```  

2. 运行移动服务安装程序：
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```  

3. 将代理注册到复制设备：
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="installation-guide-for-linux-aws-vms"></a>Linux AWS VM 安装指南

1. 将安装程序 tarball 的内容提取到 AWS VM 上的本地文件夹（例如 /tmp/MobSvcInstaller），如下所示：
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```  

2. 运行安装程序脚本：
    ```
    sudo ./install -r MS -q
    ```  

3. 将代理注册到复制设备：
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="enable-replication-for-aws-vms"></a>为 AWS VM 启用复制

> [!NOTE]
> 通过门户，可一次性最多添加 10 个 VM 用于复制。 若要同时复制更多 VM，可分批次添加，每次添加 10 个。

1. 在 Azure Migrate 项目中选择“服务器”>“Azure Migrate: 服务器迁移”，然后单击“复制”。

    ![复制 VM](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. 在“复制”>“源设置” > “你的计算机是否已虚拟化?”中，选择“未虚化/其他”   。
3. 在“本地设备”中，选择已设置的 Azure Migrate 设备的名称。
4. 在“进程服务器”中，选择复制设备的名称。 
5. 在“来宾凭据”中，请选择以前在[复制安装程序设置](#download-the-replication-appliance-installer)过程中创建的虚拟帐户，以手动安装出行服务（不支持推送安装）。 然后单击“下一页:虚拟机”。   
 
    ![复制 VM](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)
6. 在“虚拟机”中的“从评估中导入迁移设置?”内，保留默认设置“否，我将手动指定迁移设置”。  
7. 检查要迁移的每个 VM。 然后单击“下一页:目标设置”。

    ![选择 VM](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)

8. 在“目标设置”中，选择订阅以及要迁移到的目标区域，并指定迁移之后 Azure VM 所在的资源组。
9. 在“虚拟网络”中，选择迁移之后 Azure VM 要加入到的 Azure VNet/子网。
10. 在“Azure 混合权益”中：
    - 如果你不想要应用 Azure 混合权益，请选择“否”。 然后单击“下一步”  。
    - 如果你的 Windows Server 计算机享有有效软件保障或 Windows Server 订阅的权益，并且你想要将此权益应用到所要迁移的计算机，请选择“是”。 然后单击“下一步”  。

    ![目标设置](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

11. 在“计算”中，查看 VM 名称、大小、OS 磁盘类型和可用性集。 VM 必须符合 [Azure 要求](migrate-support-matrix-physical-migration.md#azure-vm-requirements)。

    - **VM 大小**：默认情况下，Azure Migrate 服务器迁移会根据 Azure 订阅中最接近的匹配项选择大小。 或者，请在“Azure VM 大小”中的手动选择一个大小。
    - **OS 磁盘**：为 VM 指定 OS（启动）磁盘。 OS 磁盘是包含操作系统引导加载程序和安装程序的磁盘。 
    - **可用性集**：如果迁移后 VM 应位于某个 Azure 可用性集中，请指定该集。 该集必须位于为迁移指定的目标资源组中。

    ![计算设置](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

12. 在“磁盘”中，指定是否要将 VM 磁盘复制到 Azure，并选择 Azure 中的磁盘类型（标准 SSD/HDD 或高级托管磁盘）。 然后单击“下一步”  。
    - 可以从复制中排除磁盘。
    - 如果排除了磁盘，迁移后，这些磁盘将不会出现在 Azure VM 中。 

    ![磁盘设置](./media/tutorial-migrate-physical-virtual-machines/disks.png)

13. 在“检查并开始复制”中检查设置，然后单击“复制”启动服务器的初始复制。 

> [!NOTE]
> 在复制开始之前，随时可以在“管理” > “复制计算机”中更新复制设置。 开始复制后无法更改设置。

## <a name="track-and-monitor-replication-status"></a>跟踪和监视复制状态

- 单击“复制”会启动一个“开始复制”作业。
- “开始复制”作业成功完成后，VM 将开始初始复制到 Azure。
- 初始复制完成后，增量复制将会开始。 对 AWS VM 磁盘所做的增量更改会定期复制到 Azure 中的副本磁盘。

可以在门户通知中跟踪作业状态。

可以通过单击“Azure Migrate: 服务器迁移”中的“正在复制服务器” 来监视复制状态。  

![监视复制](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>运行测试迁移

增量复制开始后，在运行到 Azure 的完整迁移之前，可以针对 VM 运行测试迁移。 强烈建议执行测试迁移，你可通过它在继续实际迁移之前发现潜在的问题并解决它们。 建议在迁移之前为每个 VM 至少执行一次此操作。

- 运行测试迁移可检查迁移是否按预期进行而不影响 AWS VM，它们仍可保持正常运行并继续复制。
- 测试迁移通过使用复制的数据创建一个 Azure VM 来模拟迁移（通常是迁移到 Azure 订阅中的非生产 VNet）。
- 可以使用复制的测试 Azure VM 来验证迁移、执行应用测试，并解决完整迁移之前出现的任何问题。

按如下所述执行测试迁移：

1. 在“迁移目标” > “服务器” > “Azure Migrate:  服务器迁移”中，单击“测试已迁移的服务器”。

     ![测试已迁移的服务器](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. 右键单击要测试的 VM，然后单击“测试迁移”。

    ![测试迁移](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. 在“测试迁移”中，选择 Azure VM 在迁移后所在的 Azure VNet。 建议使用非生产 VNet。
4. “测试迁移”作业随即启动。 在门户通知中监视该作业。
5. 迁移完成后，在 Azure 门户上的“虚拟机”中查看已迁移的 Azure VM。 计算机名称带有 **-Test** 后缀。
6. 测试完成后，在“复制计算机”中右键单击该 Azure VM，然后单击“清理测试迁移”。

    ![清理迁移](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-aws-vms"></a>迁移 AWS VM

确认测试迁移按预期方式进行后，就可迁移 AWS VM。

1. 在 Azure Migrate 项目中选择“服务器” > “Azure Migrate: 服务器迁移”，然后单击“复制服务器”。

    ![复制服务器](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. 在“复制计算机”中，右键单击该 VM 并选择“迁移”。
3. 在“迁移” > “关闭虚拟机并执行计划迁移(不会丢失任何数据)”中，选择“是” > “确定”。   
    - 如果你不想要关闭 VM，请选择“否”。
4. 随即会针对该 VM 启动一个迁移作业。 可单击门户页面右上角的通知铃铛图标，或转到服务器迁移工具的“作业”页面（单击工具磁贴上的“概述”，再从左侧菜单中选择“作业”）来查看作业状态。
5. 作业完成后，可从“虚拟机”页面查看和管理该 VM。

### <a name="complete-the-migration"></a>完成迁移

1. 完成迁移后，右键单击该 VM 并选择“停止迁移”。 这样会执行以下操作：
    - 停止 AWS VM 的复制。
    - 从 Azure Migrate 的“复制服务器”计数中删除 AWS VM：服务器迁移。
    - 清除 VM 的复制状态信息。
2. 在迁移的计算机上安装 [Linux](../virtual-machines/extensions/agent-linux.md) 代理。 Azure VM Windows 代理会在迁移过程中预安装。
3. 执行任何迁移后的应用调整，例如更新数据库连接字符串和 Web 服务器配置。
4. 对 Azure 中当前运行的迁移应用程序执行最终的应用程序和迁移验收测试。
5. 将流量交接到已迁移的 Azure VM 实例。
6. 更新所有内部文档，以显示新的位置和 Azure VM 的 IP 地址。 




## <a name="post-migration-best-practices"></a>迁移后的最佳做法

- 为提高恢复能力，请执行以下操作：
    - 使用 Azure 备份服务备份 Azure VM 以保证数据安全。 [了解详细信息](../backup/quick-backup-vm-portal.md)。
    - 使用 Site Recovery 将 Azure VM 复制到次要区域以保证工作负荷运行且持续可用。 [了解详细信息](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- 为提高安全性，请执行以下操作：
    - 使用 [Azure 安全中心 - 适时管理](../security-center/security-center-just-in-time.md)锁定和限制入站流量访问。
    - 使用[网络安全组](../virtual-network/security-overview.md)限制流入管理终结点的网络流量。
    - 部署[Azure 磁盘加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)以帮助保护磁盘，并保护数据以防被盗和未经授权的访问。
    - 详细了解[保护 IaaS 资源的安全](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)，并访问[Azure 安全中心](https://azure.microsoft.com/services/security-center/)。
- 为了便于监视和管理，请执行以下操作：
    - 考虑部署[Azure 成本管理](../cost-management-billing/cloudyn/overview.md)以监视资源使用率和支出。



## <a name="troubleshooting--tips"></a>疑难解答/提示

**问题：** 我在已发现的要迁移的服务器列表中看不到我的 AWS VM   
**答：** 请检查复制设备是否满足要求。 确保在要迁移的源 VM 上安装移动代理并注册配置服务器。 检查网络设置和防火墙规则，启用复制设备与源 AWS VM 之间的网络路径。  

**问题：** 如何知道我的 VM 是否已成功迁移   
**答：** 迁移后，你可从“虚拟机”页面查看和管理 VM。 连接到已迁移的 VM 进行验证。  

**问题：** 我无法从之前创建的服务器评估结果中导入 VM 进行迁移   
**答：** 目前，我们不支持为此工作流导入评估。 临时解决方法是，导出评估，然后在“启用复制”步骤期间手动选择 VM 建议。
  
**问题：** 我在尝试发现我的 AWS VM 时收到“未能提取 BIOS GUID”错误   
**答：** 请查看 AWS VM 支持的操作系统。  

**问题：** 我的复制状态为“未进行”    
**答：** 请检查复制设备是否满足要求。 确保已在复制设备 TCP 端口 9443 和 HTTPS 443 上启用了所需端口以进行数据传输。 确保没有复制设备的陈旧重复版本连接到同一项目。   

**问题：** 我无法使用 Azure Migrate 发现 AWS 实例，原因是远程 Windows 管理服务出现 HTTP 状态代码 504    
**答：** 请确保满足 Azure 迁移设备要求和 URL 访问需求。 确保没有代理设置阻止设备注册。   

## <a name="next-steps"></a>后续步骤

在 Azure 云采用框架中调查[云迁移旅程](/azure/architecture/cloud-adoption/getting-started/migrate)。
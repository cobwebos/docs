---
title: 使用 Azure Site Recovery 将本地 Windows Server 2008 服务器迁移到 Azure | Microsoft Docs
description: 本文介绍如何使用 Azure Site Recovery 将本地 Windows Server 2008 计算机迁移到 Azure。
services: site-recovery
documentationcenter: ''
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/23/2018
ms.author: bsiva
ms.openlocfilehash: 6e5946f3f9dcf1c7d941054c844adcf683b485ab
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308637"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>将运行 Windows Server 2008 的服务器迁移到 Azure

本教程介绍如何使用 Azure Site Recovery 将运行 Windows Server 2008 或 2008 R2 的本地服务器迁移到 Azure。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 准备适合迁移的本地环境
> * 设置目标环境
> * 设置复制策略
> * 启用复制
> * 运行测试迁移，确保一切按预期正常工作
> * 故障转移到 Azure 并完成迁移

“限制和已知问题”部分列出了在将 Windows Server 2008 计算机迁移到 Azure 时可能会遇到的已知问题的某些限制和解决方法。 


## <a name="supported-operating-systems-and-environments"></a>支持的操作系统和环境


|操作系统  | 本地环境  |
|---------|---------|
|Windows Server 2008 SP2 - 32 位和 64 位（IA-32 和 x86-64）</br>- Standard</br>- Enterprise</br>- Datacenter   |     VMware VM、Hyper-V VM 和物理服务器    |
|Windows Server 2008 R2 SP1 - 64 位</br>- Standard</br>- Enterprise</br>- Datacenter     |     VMware VM、Hyper-V VM 和物理服务器|

> [!WARNING]
> - 不支持迁移运行 Server Core 的服务器。
> - 确保在迁移前已安装最新的服务包和 Windows 更新。


## <a name="prerequisites"></a>先决条件

在开始之前，请查看适用于 [VMware 和物理服务器迁移](vmware-azure-architecture.md)或 [Hyper-V 虚拟机迁移](hyper-v-azure-architecture.md)的 Azure Site Recovery 体系结构 

若要迁移运行 Windows Server 2008 或 Windows Server 2008 R2 的 Hyper-V 虚拟机，请执行[将本地计算机迁移到 Azure](migrate-tutorial-on-premises-azure.md) 教程中的步骤。

本教程的其余部分介绍如何迁移运行 Windows Server 2008 或 2008 R2 的本地 VMware 虚拟机和物理服务器。


## <a name="limitations-and-known-issues"></a>限制和已知问题

- 用于迁移 Windows Server 2008 SP2 服务器的配置服务器、其他进程服务器和移动服务应该运行 9.18.0.1 版的 Azure Site Recovery 软件。 9.18.0.1 版配置服务器和进程服务器的统一安装程序可以从 [https://aka.ms/asr-w2k8-migration-setup](https://aka.ms/asr-w2k8-migration-setup) 下载。

- 现有的配置服务器或进程服务器不能用于迁移运行 Windows Server 2008 SP2 的服务器。 新的配置服务器应预配 9.18.0.1 版 Azure Site Recovery 软件。 此配置服务器只应该用于将 Windows 服务器迁移到 Azure。

- 复制运行 Windows Server 2008 SP2 的服务器时，不支持应用程序一致性恢复点和多 VM 一致性功能。 Windows Server 2008 SP2 服务器应该迁移到崩溃一致性恢复点。 默认情况下，每隔 5 分钟生成崩溃一致性恢复点。 将复制策略与已配置的应用程序一致性快照频率配合使用会导致复制运行状况出现严重问题，因为缺少应用程序一致性恢复点。 为了避免误报，请在复制策略中将应用程序一致性快照频率设置为“关”。

- 要迁移的服务器应该安装 .NET Framework 3.5 Service Pack 1，否则移动服务无法运行。


- 如果服务器有动态磁盘，你可能会在某些配置中注意到，这些磁盘在已故障转移的服务器上标记为脱机或显示为外部磁盘。 你可能还会注意到，跨动态磁盘的镜像卷的镜像集状态标记为“失败的冗余”。 可以通过 diskmgmt.msc 修复此问题，只需手动导入这些磁盘并重新激活它们即可。

- 要迁移的服务器应该安装 vmstorfl.sys 驱动程序。 如果要迁移的服务器中没有此驱动程序，故障转移可能会失败。 
  > [!TIP]
  >检查此驱动程序是否存在于“C:\Windows\system32\drivers\vmstorfl.sys”中。 如果找不到此驱动程序，可以就地创建一个虚拟文件来解决问题。 
  >
  > 打开命令提示符（“运行”>“cmd”），运行“copy nul c:\Windows\system32\drivers\vmstorfl.sys”命令

- 在运行 32 位操作系统的 Windows Server 2008 SP2 服务器故障转移或测试性故障转移到 Azure 后，可能无法立即通过 RDP 连接到该服务器。 在 Azure 门户中重启已故障转移的虚拟机，然后尝试重新连接。 如果仍然无法连接，请检查服务器是否已配置为允许远程桌面连接，并确保没有防火墙规则或网络安全组在阻止连接。 
  > [!TIP]
  > 在迁移服务器之前，强烈建议进行测试性故障转移。 确保已在每个要迁移的服务器上执行至少一次成功的测试性故障转移。 在测试性故障转移时，请连接到已进行测试性故障转移的计算机，确保一切正常。
  >
  >测试性故障转移操作是非中断性的，用于对迁移进行测试，其方法是在所选隔离网络中创建虚拟机。 与故障转移操作不同，在测试性故障转移操作过程中，数据复制可持续进行。 在准备迁移之前，可以根据需要执行多次测试性故障转移。 
  >
  >


## <a name="getting-started"></a>入门

执行以下任务，准备 Azure 订阅和本地 VMware/物理环境：

1. [准备 Azure](tutorial-prepare-azure.md)
2. 准备本地 [VMware](vmware-azure-tutorial-prepare-on-premises.md)


## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

1. 登录到 [Azure 门户](https://portal.azure.com) > **恢复服务**。
2. 单击“创建资源” > “监视和管理” > “备份和站点恢复”。
3. 在“名称”中，指定友好名称 **W2K8-migration**。 如果有多个订阅，请选择合适的一个。
4. 创建资源组 **w2k8migrate**。
5. 指定 Azure 区域。 若要查看受支持的区域，请参阅 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)中的“地域可用性”。
6. 若要从仪表板快速访问保管库，请单击“固定到仪表板”，然后单击“创建”。

   ![新保管库](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

新保管库将添加到“仪表板”中的“所有资源”下，以及“恢复服务保管库”主页面上。


## <a name="prepare-your-on-premises-environment-for-migration"></a>准备适合迁移的本地环境

- 从 [https://aka.ms/asr-w2k8-migration-setup](https://aka.ms/asr-w2k8-migration-setup) 下载配置服务器安装程序（统一安装程序）
- 按照如下所述的步骤，使用在上一步中下载的安装程序文件安装源环境。

> [!IMPORTANT]
> - 确保使用在上面的第一步下载的安装程序文件来安装并注册配置服务器。 请勿从 Azure 门户下载此安装程序文件。 [https://aka.ms/asr-w2k8-migration-setup](https://aka.ms/asr-w2k8-migration-setup) 中提供的安装程序文件是唯一支持 Windows Server 2008 迁移的版本。
>
> - 不能使用现有的配置服务器来迁移运行 Windows Server 2008 的计算机。 需使用上面提供的链接来安装新的配置服务器。
>
> - 按照下面提供的步骤安装配置服务器。 请勿尝试通过直接运行统一安装程序来执行基于 GUI 的安装过程。 这样做会导致安装尝试失败并显示一个错误，指出没有 Internet 连接。

 
1) 从门户下载保管库凭据文件：在 Azure 门户中，选择在上一步中创建的恢复服务保管库。 从保管库页面的菜单中选择“Site Recovery 基础结构” > “配置服务器”。 然后单击“+服务器”。 从打开的页面的下拉表单中选择“物理配置服务器”。 单击步骤 4 中的下载按钮以下载保管库凭据文件。

 ![下载保管库注册密钥](media/migrate-tutorial-windows-server-2008/download-vault-credentials.png) 

2) 将上一步中下载的保管库凭据文件和先前下载的统一安装程序文件复制到配置服务器计算机（将在其上安装配置服务器软件的 Windows Server 2012 R2 或 Windows Server 2016 计算机）的桌面。

3) 确保配置服务器具有 Internet 连接，并且正确配置了计算机上的系统时钟和时区设置。 下载 [MySQL 5.7](https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi) 安装程序并将其放在 *C:\Temp\ASRSetup* 中（如果该目录不存在，则创建该目录。） 

4) 使用以下行创建 MySQL 凭据文件，并将其放入桌面上的 **C:\Users\Administrator\MySQLCreds.txt** 中。 将下面的“Password~1”替换为合适的强密码：

```
[MySQLCredentials]
MySQLRootPassword = "Password~1"
MySQLUserPassword = "Password~1"
```

5) 运行以下命令，将下载的统一安装程序文件的内容提取到桌面：

```
cd C:\Users\Administrator\Desktop

MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Users\Administrator\Desktop\9.18
```
  
6) 执行以下命令，使用提取的内容安装配置服务器软件：

```
cd C:\Users\Administrator\Desktop\9.18.1

UnifiedSetup.exe /AcceptThirdpartyEULA /ServerMode CS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /MySQLCredsFilePath "C:\Users\Administrator\Desktop\MySQLCreds.txt" /VaultCredsFilePath <vault credentials file path> /EnvType VMWare /SkipSpaceCheck
```

## <a name="set-up-the-target-environment"></a>设置目标环境

选择并验证目标资源。

1. 单击“准备基础结构” > “目标”，并选择要使用的 Azure 订阅。
2. 指定资源管理器部署模型。
3. Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。


## <a name="set-up-a-replication-policy"></a>设置复制策略

1. 若要创建新的复制策略，请单击“Site Recovery 基础结构” > “复制策略” > “+ 复制策略”。
2. 在“创建复制策略”中指定策略名称。
3. 在“RPO 阈值”中，指定恢复点目标 (RPO) 限制。 如果复制 RPO 超出此限制，则会生成警报。
4. 在“恢复点保留期”中，指定每个恢复点的保留期时长（以小时为单位）。 可以将复制的虚拟机恢复到窗口中的任何点。 复制到高级存储的计算机最多支持 24 小时的保留期，复制到标准存储的计算机最多支持 72 小时的保留期。
5. 在“应用一致性快照频率”中，请指定“关”。 单击“确定”创建该策略。

此策略自动与配置服务器关联。

> [!WARNING]
> 确保在复制策略的“应用一致性快照频率”设置中指定“关”。 在复制运行 Windows Server 2008 的服务器时，仅支持崩溃一致性恢复点。 为“应用一致性快照频率”指定任何其他值时，由于缺少应用一致性恢复点，会导致服务器的复制运行状况出现严重问题，因此会生成假警报。

   ![创建复制策略](media/migrate-tutorial-windows-server-2008/create-policy.png)

## <a name="enable-replication"></a>启用复制

为要迁移的 Windows Server 2008 SP2/Windows Server 2008 R2 SP1 服务器[启用复制](physical-azure-disaster-recovery.md#enable-replication)。
   
   ![添加物理服务器](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![启用复制](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

## <a name="run-a-test-migration"></a>运行测试迁移

可以在初始复制完成且服务器状态转为“受保护”后，执行对复制服务器的测试性故障转移。

运行 [测试故障转移](tutorial-dr-drill-azure.md) ，确保一切如预期正常运行。

   ![测试故障转移](media/migrate-tutorial-windows-server-2008/testfailover.png)


## <a name="migrate-to-azure"></a>迁移到 Azure

为想要迁移的计算机运行故障转移。

1. 在“设置” > “复制的项”中，单击计算机 >“故障转移”。
2. 在“故障转移”中，选择要故障转移到的“恢复点”。 选择最新恢复点。
3. 选择“在开始故障转移前关闭计算机”。 Site Recovery 在触发故障转移之前会尝试关闭服务器。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。
4. 检查 Azure VM 是否在 Azure 中按预期显示。
5. 在“复制的项”中，右键单击 VM >“完成迁移”。 该操作将完成迁移过程、停止 VM 的复制，并停止对 VM 的 Site Recovery 计费。

   ![完成迁移](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **请勿取消正在进行的故障转移**：在故障转移开始前，VM 复制已停止。 如果取消正在进行的故障转移，故障转移会停止，但 VM 将不再进行复制。
